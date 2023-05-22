---
title: "HackThisSite - Desafio Realista 7"
classes: wide
header:
  teaser: /assets/images/Desafio-7/00.jpg
  overlay_image: /assets/images/Desafio-7/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateBlue
excerpt: "Write Up do desafio realista 7 do HackThisSite"
description: "Write Up do desafio realista 7 do HackThisSite"
categories:
  - Write Up
tags:
  - HackThisSite
  - LFI
  - Apache

toc: false
toc_sticky: true
toc_label: "On This Blog"
toc_icon: "biohazard"
---

![](/assets/images/Desafio-7/00.png){: .align-center}

# Objetivo

Basicamente o objetivo desse desafio é invadir um site que tem como conteúdo o incentivo a crimes de ódio; na descrição do desafio podemos ver a seguinte frase:

"Há uma seção de administração nesse site em algum lugar, talvez escondida entre a estrutura de diretórios. Seria uma grande luta contra a tirania moral e uma vitória pela liberdade se você pudesse de alguma forma invadir o site deles."

Então vamos lá

# Coleta de informações

Acessando o site nos deparamos com uma página bem simples
  
![](/assets/images/Desafio-7/01.png){: .align-center}

A primeira ação realizada foi analisar o código fonte da aplicação afim de obter mais informações
  
![](/assets/images/Desafio-7/02.png){: .align-center}

Um ponto me chamou atenção: a aplicação utiliza um arquivo php para ler o conteúdo de outros arquivos no formato txt, o que torna esse ponto um bom candidato pra um possível LFI
  
![](/assets/images/Desafio-7/03.png){: .align-center}

Continuei analisando o código e não existem muitas funcionalidades, então decidi abrir uma das fotos para analisar o caminho dos arquivos
  
![](/assets/images/Desafio-7/04.png){: .align-center}

No passo seguinte, tentei ler o conteúdo do diretório images e descobri que esse diretório estava com a listagem habilitada
  
![](/assets/images/Desafio-7/05.png){: .align-center}

Nessa listagem, foi possível encontrar também uma pasta de nome admin, porém ao tentar acessar a pasta a aplicação pede por uma autenticação
  
![](/assets/images/Desafio-7/06.png){: .align-center}

É possível notar que essa autenticação é feita utilizando o Basic Auth

# Exploração

Para o Basic Auth funcionar corretamente é preciso habilitar esse recurso em um arquivo chamado ".htaccess", também é necessário criar um arquivo contendo as credenciais válidas para acesso, e o nome desse arquivo também deve ser informado em ".htaccess" para que a aplicação possa fazer a validação

Sabendo que provavelmente esse arquivo está localizado dentro do diretório que solicitou a autenticação, utilizei aquele possível ponto vulnerável a LFI para tentar realizar a leitura do arquivo
  
![](/assets/images/Desafio-7/07.png){: .align-center}

A aplicação retornou algumas imagens quebradas, porém analisando o código fonte da aplicação é possível identificar o conteúdo do ".htaccess"
  
![](/assets/images/Desafio-7/08.png){: .align-center}

Dessa forma, foi possível identificar que o arquivo contendo a senha dos usuários autorizados a realizar a autenticação está salva no arquivo ".htpasswd", então, realizei uma nova consulta pra tentar ler o conteúdo desse arquivo
  
![](/assets/images/Desafio-7/09.png){: .align-center}

Novamente a aplicação retornou uma imagem quebrada, porém analisando o código fonte é possível identificar o conteúdo dessa "imagem"
  
![](/assets/images/Desafio-7/10.png){: .align-center}

Dessa forma, foi possível descobrir o nome do usuário e o hash da senha utilizado para se autenticar na aplicação. O próximo passo foi tentar realizar a quebra dessa hash

Para isso utilizei o john, e juntamente com a wordlist rockyou, foi possível descobrir a senha do administrator
  
![](/assets/images/Desafio-7/11.png){: .align-center}

Agora que já sei o usuário e a senha, bastou voltar a aplicação para realizar o login

![](/assets/images/Desafio-7/12.png){: .align-center}