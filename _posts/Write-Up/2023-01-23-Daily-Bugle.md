---
title: "TryHackMe - Daily Bugle"
classes: wide
header:
  teaser: /assets/images/Daily-Bugle/00.jpg
  overlay_image: /assets/images/Daily-Bugle/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateBlue
excerpt: "Write Up da máquina Daily Bugle do TryHackMe"
description: "Write Up da máquina Daily Bugle do TryHackMe"
categories:
  - Write Up
tags:
  - TryHackMe
  - Joomla
  - SQL Injection
toc: false
toc_sticky: true
toc_label: "On This Blog"
toc_icon: "biohazard"
---

![1](/assets/images/Daily-Bugle/01.png){: .align-center}

# Objetivo

O objetivo dessa máquina é comprometer uma conta de uma aplicação que está utilizando o CMS Joomla via SQLi, praticar cracking de hashes e escalar privilégios explorando o yum

# Enumeração

Iniciando o processo de enumeração, utilizei o nmap para descobrir quais portas estavam abertas no host

![2](/assets/images/Daily-Bugle/02.png){: .align-center}

Como pode ser visto no resultado do nmap, o host possuí a porta 80 aberta, então acessei esse serviço utilizando meu navegador para descobrir o que está sendo executado no servidor web

![3](/assets/images/Daily-Bugle/03.png){: .align-center}

Acessando o servidor web, encontramos uma aplicação que parece ser um blog

Então, comecei o processo para buscar mais informações sobre essa aplicação web. O primeiro passo foi analisar o arquivo robots.txt; nele foi possível encontrar alguns diretórios interessantes presentes na aplicação e também foi possível descobrir que a aplicação está usando o CMS Joomla

![4](/assets/images/Daily-Bugle/04.png){: .align-center}

Após a leitura do robots.txt, iniciei o processo de enumeração de diretórios, porém o resultado foi bem parecido com o conteúdo presente no robots

![5](/assets/images/Daily-Bugle/05.png){: .align-center}

Como já sei que a aplicação está utilizando Joomla, utilizei o joomscan para obter mais informações sobre a aplicação, assim foi possível descobrir a versão do Joomla que está sendo utilizado pelo blog

![6](/assets/images/Daily-Bugle/06.png){: .align-center}

Sabendo a versão exata do Joomla, iniciei a procura por vulnerabilidades nessa versão do CMS e foi possível descobrir que ela é vulnerável a SQL Injection

https://www.exploit-db.com/exploits/44227

![7](/assets/images/Daily-Bugle/07.png){: .align-center}

Analisando a POC presente no exploit-db, foi possível detectar que realmente a aplicação estava vulnerável a SQLi

Para o teste foi utilizado o payload:

```
/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]="
```

![8](/assets/images/Daily-Bugle/08.png){: .align-center}

# Explorção

Sabendo que a aplicação está vulnerável a SQL Injection, então iniciei o processo de exploração dessa vulnerabilidade

O primeiro passo foi tentar descobrir o nome da tabela do banco de dados que está sendo utilizado, para isso utilizei o payload abaixo:

```
1,extractvalue(0x0a,concat(0x0a,(select/**/concat(0x7e7e7e,hex(table_name),0x7e7e7e)/**/from/**/information_schema.tables/**/where/**/table_schema=database()/**/limit/**/0,1)))=1
```

![9](/assets/images/Daily-Bugle/09.png){: .align-center}

Esse payload retorna o nome da tabela em hexadecimal, então basta converter o resultado para descobrir o nome da tabela

![10](/assets/images/Daily-Bugle/10.png){: .align-center}

Agora que já sei o nome da tabela, busquei descobrir o nome do usuário presente nela e para isso usei esse payload:

```
1,extractvalue(0x0a,concat(0x0a,(select/**/concat(0x7e7e7e,substring(username,1,20),0x7e7e7e)/**/from/**/fb6j5_users/**/limit/**/0,1)))=1
```

![11](/assets/images/Daily-Bugle/11.png){: .align-center}

Agora já tenho o nome do usuário: jonah, o próximo passo agora é descobrir sua senha

Então, realizei uma nova consulta, dessa ver utilizando o seguinte payload:

```
1,extractvalue(0x0a,concat(0x0a,(select/**/concat(0x7e7e7e,substring(password,1,20),0x7e7e7e)/**/from/**/fb6j5_users/**/limit/**/0,1)))=1
```

![12](/assets/images/Daily-Bugle/12.png){: .align-center}

Devido a limitação de exibição da página, o resultado desse payload só mostra uma parte da hash da senha do usuário (20 caracteres), que é exatamente a quantidade de caracteres que o payload está solicitando como é possível ver no comando presente no próprio payload: "substring(password,1,20)"

Então é necessário executar mais de uma requisição, alterando essa parte para visualizar o restante da hash, então uma nova solicitação é feita com "substring(password,21,40)", depois outra com  "substring(password,41,50)" e assim sucessivamente até conseguir a hash completa

Feito isso, após conseguir a hash completa, joguei o seu conteúdo para um arquivo 

![13](/assets/images/Daily-Bugle/13.png){: .align-center}

E depois utilizando o john e a wordlist rockyou, foi possível quebrar essa hash e descobrir a senha do usuário jonah

![14](/assets/images/Daily-Bugle/14.png){: .align-center}

Agora que já tenho um usuário e uma senha válidos, fui até o diretório "/administrator/" e utilizando essas credenciais, foi possível acessar o painel de controle da aplicação como administrador

![15](/assets/images/Daily-Bugle/15.png){: .align-center}

Nesse momento já tenho acesso ao painel da aplicação, o próximo passo é evoluir isso para um acesso via shell e para isso vou subir um arquivo na aplicação contendo uma reverse shell em php

Para conseguir enviar a minha reverse shell para a aplicação, é necessário ir até a área de templates do painel de controle

![16](/assets/images/Daily-Bugle/16.png){: .align-center}

E então, selecionar o template que está sendo usado pela aplicação, nesse naso o template usado é o "protostar"

![17](/assets/images/Daily-Bugle/17.png){: .align-center}

Ao acessar o template, é possível editar diversos arquivos presentes no site, escolhi o arquivo "index.php" e coloquei nele o código da reverse shell em php

![18](/assets/images/Daily-Bugle/18.png){: .align-center}

Agora, basta acessar a página principal da aplicação para executar a reverse shell e receber a conexão 

![19](/assets/images/Daily-Bugle/19.png){: .align-center}

A primeira coisa que tentei foi ir direto no diretório home do usuário para pegar a flag, mas ao tentar acessar a pasta descobri que não tinha premissão para ver seu conteúdo

![20](/assets/images/Daily-Bugle/20.png){: .align-center}

Então, comecei a buscar no sistema algum arquivo que pudesse conter alguma informação que me ajudasse a escalar meus privilégios

Normalmente, os servidores possuem arquivos de configuração e nesse arquivos estão presentes credênciais de acesso ao banco de dados. Isso acontece porque normalmente sites que usam mysql ou algum outro banco de dados possuem um arquivo para conexão para tornar a aplicação mais segura,  isso evita reciclar código e credenciais em cada página e otimiza o tempo do programador.

Sabendo disso, fui direto ao diretório "/var/www/html" e comecei a analisar os arquivos para tentar encontrar esse arquivo de configuração, e como esperado, ao ler o conteúdo do arquivo "configuration.php" foi possível encontrar a senha do banco de dados

![21](/assets/images/Daily-Bugle/21.png){: .align-center}

Imaginei que essa senha também poderia ser a mesma senha usada pelo usuário, então tentei me autenticar como jjameson utilizando essa credencial, e como esperado, foi possível mudar meu usuário

![22](/assets/images/Daily-Bugle/22.png){: .align-center}

Agora que consegui um acesso um pouco melhor, comecei uma nova enumeração para tentar escalar meus privilégios e conseguir um acesso como root

Comecei verificando se esse novo usuário tinha premissão para executar algum programa utilizando o sudo, e descobri que ele podia executar o yum

![23](/assets/images/Daily-Bugle/23.png){: .align-center}

Então, procurei no GTFOBins se havia alguma maneira de escalar privilégions me aproveitando dessa permissão e descobri que existe uma forma de fazer isso quando o usuário pode executar ele como sudo, e esse é exatamente o cenário que eu tenho

![24](/assets/images/Daily-Bugle/24.png){: .align-center}

Bastou então replicar o passo a passo presente no GTFOBins e conseguir o acesso a máquina como usuário root

![26](/assets/images/Daily-Bugle/25.png){: .align-center}