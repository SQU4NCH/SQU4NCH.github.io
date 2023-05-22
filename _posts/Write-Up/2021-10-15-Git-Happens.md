---
title: "TryHackMe - Git Happens"
classes: wide
header:
  teaser: /assets/images/Git-Happens/00.jpg
  overlay_image: /assets/images/Git-Happens/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateBlue
excerpt: "Write Up da máquina Git Happens do TryHackMe"
description: "Write Up da máquina Git Happens do TryHackMe"
categories:
  - Write Up
tags:
  - TryHackMe
  - Git
toc: false
toc_sticky: true
toc_label: "On This Blog"
toc_icon: "biohazard"
---

![1](/assets/images/Git-Happens/Capa.png){: .align-center}

# Objetivo

O objetivo dessa máquina é encontrar a senha da aplicação, então vamos lá

# Enumeração

Apesar do nome da sala ser bem sugestivo, vou seguir o costume e rodar o nmap para enumerar a máquina

![2](/assets/images/Git-Happens/nmap.png){: .align-center}

No resultado do nmap podemos ver que somente a porta 80 está aberta na máquina e também nos mostra que ele encontrou um diretório chamado .git

# Exploração

Vou usar uma ferramenta chamada git-dumper para baixar esse repositório git pra minha máquina local

*Pra baixar a ferramenta: (https://github.com/arthaud/git-dumper)*

![3](/assets/images/Git-Happens/git-dumper1.png){: .align-center}
![4](/assets/images/Git-Happens/git-dumper2.png){: .align-center}

Agora que o repositório do git já está na minha máquina eu posso ver todos os commits que foram feitos na aplicação

![5](/assets/images/Git-Happens/git-log.png){: .align-center}

Entre os commits realizados teve um que me chamou a atenção, a descrição dele é: "Ofuscou o código-fonte. Espero que a segurança seja feliz!", vamos ver o que tem nele

![6](/assets/images/Git-Happens/commit.png){: .align-center}

Entre as partes removidas do código está o script de verificação de usuário e senha, nele podemos ver a senha da aplicação que está em claro

**Pra entender como o Git funciona: (https://git-scm.com/book/pt-br/v2)**