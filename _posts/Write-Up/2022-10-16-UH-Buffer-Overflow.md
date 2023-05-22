---
title: "Buffer Overflow"
classes: wide
header:
  teaser: /assets/images/Buffer-Overflow/00.jpg
  overlay_image: /assets/images/Buffer-Overflow/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateBlue
excerpt: "Exploração simples de Buffer Overflow vanilla"
description: "Exploração simples de Buffer Overflow vanilla"
categories:
  - Exploitation
tags:
  - Buffer Overflow
  - Pwn
toc: false
toc_sticky: true
toc_label: "On This Blog"
toc_icon: "biohazard"
---

# Objetivo

O objetivo do desafio é fazer uma alteração na execução do programa para chamar a função "vitoria"

  

# Execução

Analisando o código fonte, é possível notar que a função não é chamada em nenhum momento durante a execução do programa

  
![](/assets/images/Buffer-Overflow/01.png){: .align-center}

Então, utilizando o OllyDbg, comecei a analisar a sua execução e durante o processo, encontrei o endereço de memória da função que eu quero chamar

  
![](/assets/images/Buffer-Overflow/02.png){: .align-center}

A primeira informação obtida é que o endereço da função vitoria é 00401350

Como tenho acesso ao código da aplicação, sei que a variável está esperando receber 50 caracteres no máximo, então enviei os 50 para ver como ficava essa variável na memória

  
![](/assets/images/Buffer-Overflow/03.png){: .align-center}

Analisando a pilha, podemos ver o espaço reservado para receber o conteúdo da variável

  
![](/assets/images/Buffer-Overflow/04.png){: .align-center}

Como podemos ver na imagem, o endereço de retorno está localizado em 0060FF0C, então para sobrescrever o endereço de retorno é preciso enviar mais 16 caracteres

Enviando então, os 66 caracteres a aplicação retorna um erro

  
![](/assets/images/Buffer-Overflow/05.png){: .align-center}

E como pode ser visto nos registradores, EIP foi sobrescrito

  
![](/assets/images/Buffer-Overflow/06.png){: .align-center}

Agora que já tenho a quantidade de caracteres que preciso enviar, e como já sei o endereço de memória da função vitoria, basta substituir os últimos 4 bytes pelo endereço e enviar para aplicação

Usando o python, criei um programa bem simples para criar meu payload

  
![](/assets/images/Buffer-Overflow/08.png){: .align-center}

Por fim, enviei essa saída para o programa vulnerável, e assim consegui resolver o desafio

  
![](/assets/images/Buffer-Overflow/09.png){: .align-center}