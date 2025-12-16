---
date: 2022-04-19 23:48:05
layout: post
title: TryHackMe - Net Sec Challenger
subtitle: 
description: >-
  Write Up da máquina Net Sec Challenger do TryHackMe.
image: >-
  /assets/img/Net-Sec-Challenger/00.jpg
optimized_image: >-
  /assets/img/Net-Sec-Challenger/00.jpg
category: Write Up
tags:
  - TryHackMe
  - Redes
author: squ4nch
paginate: false
---

![](/assets/img/Net-Sec-Challenger/logo.png){: .align-center}

# Objetivo

O objetivo dessa máquina é praticar alguns conceitos do modulo de segurança de rede

# Enumeração

Usando o nmap, fiz uma varredura em todas as portas para saber quais estavam abertas e esse foi o resultado:

~$ nmap -sS -Pn 10.10.223.210 -p-

![](/assets/img/Net-Sec-Challenger/nmap.png){: .align-center}

Com esse resultado já é possível responder as 3 primeiras perguntas

Então chegou a hora de começar a interagir com os serviços e pegar as flags, o primeiro deles é o servidor HTTP
  
![](/assets/img/Net-Sec-Challenger/http.png){: .align-center}

O próximo serviço que vamos interagir é o SSH
  
![](/assets/img/Net-Sec-Challenger/ssh.png){: .align-center}

E por último o serviço de FTP, que está rodando na porta 10021
  
![](/assets/img/Net-Sec-Challenger/ftp.png){: .align-center}

Somos informados que através de engenharia social conseguiram descobrir 2 nomes de usuários: eddie e quinn

Com essa informação, criei um arquivo com esses 2 nomes e usei o hydra para descobrir a senha de acesso deles ao serviço FTP
  
![](/assets/img/Net-Sec-Challenger/hydra.png){: .align-center}

Agora descobri a senha para acessar o FTP, vou me logar e pegar a flag.

O usuário eddie não tem nada de relevante, então segui para o usuário quinn e encontrei o arquivo com a flag e fiz o download para a minha máquina
  
![](/assets/img/Net-Sec-Challenger/flag-ftp.png){: .align-center}

Na minha máquina agora é só ler o arquivo
  
![](/assets/img/Net-Sec-Challenger/cat-flag.png){: .align-center}

Como último desafio, temos que acessar o host na porta 8080 e concluir o desafio que ele pede lá
  
![](/assets/img/Net-Sec-Challenger/web.png){: .align-center}

O objetivo é fazer uma varredura usando o nmap, da forma mais discreta possível para não ser detectado pelo IDS

Com isso em mente eu realizei o seguinte scan:
  
![](/assets/img/Net-Sec-Challenger/nmap-evasion.png){: .align-center}

E esse foi o resultado:
  
![](/assets/img/Net-Sec-Challenger/web-evasion.png){: .align-center}