---
title: "Cross-site Script labs"
classes: wide
header:
  teaser: /assets/images/XSS-PortSwigger/00.jpg
  overlay_image: /assets/images/XSS-PortSwigger/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateBlue
excerpt: "Write Up de alguns labs sobre Cross-site Script da PortSwigger"
description: "Write Up de alguns labs sobre Cross-site Script da PortSwigger"
categories:
  - Write Up
tags:
  - PortSwigger
  - Cross-site Script
  - XSS
toc: true
toc_sticky: true
toc_label: "On This Blog"
toc_icon: "biohazard"
---

# Reflected DOM XSS
## Objetivo

![](/assets/images/XSS-PortSwigger/A-01.png){: .align-center}

## Execução

Acessando o blog é possível realizar uma consulta para encontrar postagens

Analisando as requisições é possível ver que a aplicação realiza duas requisições com o valor passado, a primeira somente passando o parâmetro da pesquisa

![](/assets/images/XSS-PortSwigger/A-02.png){: .align-center}

A segunda passando o parâmetro pra uma função, essa função retorna o resultado da pesquisa e esse resultado é refletido na página posteriormente

![](/assets/images/XSS-PortSwigger/A-03.png){: .align-center}

Na primeira requisição é possível encontrar o arquivo Javascript que contem a função que realiza a pesquisa

Analisando o conteúdo desse arquivo, é possível descobrir que o valor enviado é recebido em uma função "eval" e depois o valor é refletido em uma tag "h1"

![](/assets/images/XSS-PortSwigger/A-04.png){: .align-center}

Sabendo do funcionamento da função, realizei uma nova consulta, dessa vez enviando um payload para que a função "alert" fosse refletida na página

![](/assets/images/XSS-PortSwigger/A-05.png){: .align-center}

Dessa forma o código foi executado sem nenhum problema

![](/assets/images/XSS-PortSwigger/A-06.png){: .align-center}

E assim foi possível resolver o lab

![](/assets/images/XSS-PortSwigger/A-07.png){: .align-center}

# Reflected XSS into a JavaScript string with angle brackets HTML encoded
## Objetivo

![](/assets/images/XSS-PortSwigger/B-01.png){: .align-center}

## Execução

Acessando o site do blog, é possível encontrar um campo para realizar pesquisa

![](/assets/images/XSS-PortSwigger/B-02.png){: .align-center}

Então, realizei uma pesquisa para entender como a aplicação se comporta, e interceptei essa requisição com o burp

![](/assets/images/XSS-PortSwigger/B-03.png){: .align-center}

Analisando a requisição, foi possível descobrir que o valor enviado através da consulta é refletido em dois lugares na página

![](/assets/images/XSS-PortSwigger/B-04.png){: .align-center}

O segundo lugar, está dentro de uma tag "script". Então enviei uma nova consulta, dessa vez passando a função alert

![](/assets/images/XSS-PortSwigger/B-05.png){: .align-center}

Como é possível ver na resposta da requisição, a função foi incorporada dentro da tag "script", novamente voltei ao blog e enviei o novo payload, e o código foi executado

![](/assets/images/XSS-PortSwigger/B-06.png){: .align-center}

Dessa forma, foi possível resolver o desafio

![](/assets/images/XSS-PortSwigger/B-07.png){: .align-center}

# Reflected XSS into attribute with angle brackets HTML-encoded
## Objetivo

![](/assets/images/XSS-PortSwigger/C-01.png){: .align-center}

## Execução

Acessando o blog é possível encontrar um campo para pesquisa

![](/assets/images/XSS-PortSwigger/C-02.png){: .align-center}

Então, realizei uma pesquisa para entender como a aplicação funciona

![](/assets/images/XSS-PortSwigger/C-03.png){: .align-center}

Analisando a requisição, é possível perceber que o valor enviado é refletido em dois lugares na página

![](/assets/images/XSS-PortSwigger/C-04.png){: .align-center}

Sabendo disso, realizei uma nova requisição, dessa vez enviando o meu payload

![](/assets/images/XSS-PortSwigger/C-05.png){: .align-center}

Como é possível ver no resultado da requisição, o payload enviado foi refletido dentro da tag input, então retornei para a aplicação e realizei uma nova consulta, passando o mesmo payload e como esperado o código é executado

![](/assets/images/XSS-PortSwigger/C-06.png){: .align-center}

Dessa forma, foi possível resolver o lab

![](/assets/images/XSS-PortSwigger/C-07.png){: .align-center}

# Stored XSS into anchor href attribute with double quotes HTML-encoded
## Objetivo

![](/assets/images/XSS-PortSwigger/D-01.png){: .align-center}

## Execução

Acessando um post do blog, é possível enviar um comentário passando algumas informações

![](/assets/images/XSS-PortSwigger/D-02.png){: .align-center}

Então, enviei um comentário para entender como a aplicação se comporta, e analisando o resultado é possível ver que o website informado é armazenado dentro de um "href"

![](/assets/images/XSS-PortSwigger/D-03.png){: .align-center}

Com essa informação, enviei um novo comentário, dessa vez enviado como endereço do site o payload "javascript:alert(0)"

![](/assets/images/XSS-PortSwigger/D-04.png){: .align-center}

E como é possível ver, o conteúdo do site foi novamente armazenado em um "href"

![](/assets/images/XSS-PortSwigger/D-05.png){: .align-center}

Com isso, ao clicar no nome do usuário, o código javascript é executado

![](/assets/images/XSS-PortSwigger/D-06.png){: .align-center}

E dessa forma foi possível resolver o lab

![](/assets/images/XSS-PortSwigger/D-07.png){: .align-center}
