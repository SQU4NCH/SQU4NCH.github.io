---
date: 2021-10-15 23:48:05
layout: post
title: TryHackMe - Git Happens
subtitle: 
description: >-
  Write Up da máquina Git Happens do TryHackMe.
image: >-
  /assets/img/Git-Happens/00.jpg
optimized_image: >-
  /assets/img/Git-Happens/00.jpg
category: Write Up
tags:
  - TryHackMe
  - Git
author: squ4nch
paginate: false
---

![1](/assets/img/Git-Happens/Capa.png){: .align-center}

# Objetivo

O objetivo dessa máquina é encontrar a senha da aplicação, então vamos lá

# Enumeração

Apesar do nome da sala ser bem sugestivo, vou seguir o costume e rodar o nmap para enumerar a máquina

![2](/assets/img/Git-Happens/nmap.png){: .align-center}

No resultado do nmap podemos ver que somente a porta 80 está aberta na máquina e também nos mostra que ele encontrou um diretório chamado .git

# Exploração

Vou usar uma ferramenta chamada git-dumper para baixar esse repositório git pra minha máquina local

*Pra baixar a ferramenta: (https://github.com/arthaud/git-dumper)*

![3](/assets/img/Git-Happens/git-dumper1.png){: .align-center}
![4](/assets/img/Git-Happens/git-dumper2.png){: .align-center}

Agora que o repositório do git já está na minha máquina eu posso ver todos os commits que foram feitos na aplicação

![5](/assets/img/Git-Happens/git-log.png){: .align-center}

Entre os commits realizados teve um que me chamou a atenção, a descrição dele é: "Ofuscou o código-fonte. Espero que a segurança seja feliz!", vamos ver o que tem nele

![6](/assets/img/Git-Happens/commit.png){: .align-center}

Entre as partes removidas do código está o script de verificação de usuário e senha, nele podemos ver a senha da aplicação que está em claro

**Pra entender como o Git funciona: (https://git-scm.com/book/pt-br/v2)**