---
date: 2022-12-22 23:48:05
layout: post
title: Buffer Overflow (Exec. Comando)
subtitle: 
description: >-
  Exploração simples de Buffer Overflow para execução de comandos.
image: >-
  /assets/img/Buffer-Overflow-Exec/00.jpg
optimized_image: >-
  /assets/img/Buffer-Overflow-Exec/00.jpg
category: Exploitation
tags:
  - Buffer Overflow
  - Pwn
author: squ4nch
paginate: false
---

# Objetivo

O objetivo desse desafio é explorar o programa vulnerável para executar algum comando no computador

# Execução

Analisando o código da aplicação, podemos ver que o programa tem um código bem simples
  
![](/assets/img/Buffer-Overflow-Exec/01.png){: .align-center}

Como já sabemos que o programa está vulnerável a Buffer Overflow, vou direto descobrir quantos bytes são necessários para sobreescrever o endereço de retorno

Para isso, vou utilizar o Buffer Overflow Pattern Generation para criar um Pattern contendo 600 caracteres que serão enviados para a aplicação
  
![](/assets/img/Buffer-Overflow-Exec/02.png){: .align-center}

Enviando esse conteúdo para o programa, ele retorna um erro e é possível ver o valor presente em EIP no momento em que isso acontece
  
![](/assets/img/Buffer-Overflow-Exec/03.png){: .align-center}

Com o valor presente em EIP, foi possível descobrir que é necessário enviar 512 bytes para atingir o endereço de retorno
  
![](/assets/img/Buffer-Overflow-Exec/04.png){: .align-center}

Agora que já é possível controlar o endereço de retorno da aplicação, é preciso criar o payload contendo o comando que será executado no sistema

Para isso, vou utilizar o msfvenom e criar um shellcode que, ao ser executado, abra a calculadora do windows
  
![](/assets/img/Buffer-Overflow-Exec/05.png){: .align-center}

Com o shellcode pronto, juntei todas as informações descobertas até agora e criei um "exploit" usando o python para explorar a aplicação
  
![](/assets/img/Buffer-Overflow-Exec/06.png){: .align-center}

Por fim, executando o exploit e enviando a saída dele para o programa vulnerável, o sistema abre a calculadora
  
![](/assets/img/Buffer-Overflow-Exec/07.png){: .align-center}