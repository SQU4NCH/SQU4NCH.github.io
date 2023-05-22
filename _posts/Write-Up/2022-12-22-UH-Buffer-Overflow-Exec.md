---
title: "Buffer Overflow (Exec. Comando)"
classes: wide
header:
  teaser: /assets/images/Buffer-Overflow-Exec/00.jpg
  overlay_image: /assets/images/Buffer-Overflow-Exec/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateBlue
excerpt: "Exploração simples de Buffer Overflow para execução de comandos"
description: "Exploração simples de Buffer Overflow para execução de comandos"
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

O objetivo desse desafio é explorar o programa vulnerável para executar algum comando no computador

  

# Execução

Analisando o código da aplicação, podemos ver que o programa tem um código bem simples

  
![](/assets/images/Buffer-Overflow-Exec/01.png){: .align-center}

Como já sabemos que o programa está vulnerável a Buffer Overflow, vou direto descobrir quantos bytes são necessários para sobreescrever o endereço de retorno

Para isso, vou utilizar o Buffer Overflow Pattern Generation para criar um Pattern contendo 600 caracteres que serão enviados para a aplicação

  
![](/assets/images/Buffer-Overflow-Exec/02.png){: .align-center}

Enviando esse conteúdo para o programa, ele retorna um erro e é possível ver o valor presente em EIP no momento em que isso acontece

  
![](/assets/images/Buffer-Overflow-Exec/03.png){: .align-center}

Com o valor presente em EIP, foi possível descobrir que é necessário enviar 512 bytes para atingir o endereço de retorno

  
![](/assets/images/Buffer-Overflow-Exec/04.png){: .align-center}

Agora que já é possível controlar o endereço de retorno da aplicação, é preciso criar o payload contendo o comando que será executado no sistema

Para isso, vou utilizar o msfvenom e criar um shellcode que, ao ser executado, abra a calculadora do windows

  
![](/assets/images/Buffer-Overflow-Exec/05.png){: .align-center}

Com o shellcode pronto, juntei todas as informações descobertas até agora e criei um "exploit" usando o python para explorar a aplicação

  
![](/assets/images/Buffer-Overflow-Exec/06.png){: .align-center}

Por fim, executando o exploit e enviando a saída dele para o programa vulnerável, o sistema abre a calculadora

  
![](/assets/images/Buffer-Overflow-Exec/07.png){: .align-center}