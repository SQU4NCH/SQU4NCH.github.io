---
title: "O que exatamente é um sistema operacional?"
classes: wide
header:
  teaser: /assets/images/so/00.jpg
  overlay_image: /assets/images/so/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateGray
excerpt: "Nessa postagem mostro de forma breve, três perspectivas diferentes sobre o papel do sistema operacional"
description: "Nessa postagem mostro de forma breve, três perspectivas diferentes sobre o papel do sistema operacional"
categories:
  - Operating System
tags:
  - Sistemas Operacionais
toc: false
toc_sticky: true
toc_label: "Sumário"
toc_icon: "terminal"
---

Muitas vezes, os fornecedores de sistemas operacionais chamam tudo o que está incluído na sua distribuição como sistema operacional. Mas essa é uma definição um pouco errada por que os arquivos de som e os programas de aplicação não fazem parte do sistema operacional em si

Por outro lado, o nome sistema operacional, fica restrito a um programa executado em hardware puro, sem nenhum software de suporte. Esse programa, muitas vezes chamado de kernel, não fornece todas as funções de um sistema operacional

Definir um sistema operacional em termos de como ele é "embalado", parece ser uma função quase impossível. Podemos então, definir um sistema operacional em termos do que ele faz. Assim podemos identificar as seguintes funções principais:

- Gerenciar o compartilhamento de recursos entre entidades concorrentes
- Fornecer serviços comuns que facilitam o desenvolvimento de aplicações
- Servir como interface entre programas de aplicação e o hardware

Com essas ideias em mente podemos definir o sistema operacional como:

>"Um conjunto de um ou mais programas que fornece um conjunto de serviço, o qual cria uma interface entre aplicações e o hardware do computador e que aloca e gerencia recursos compartilhados entre múltiplos processos"

As três funções citadas acima correspondem a três perspectivas diferentes sobre o papel do sistema operacional. Vamos analisar então essas três perspectivas 

## Gerenciador de recursos

O modo clássico de encarar um sistema operacional é como um **gerenciador de recursos**. Desse ponto de vista, o sistema operacional é responsável pelo hardware do sistema. Nesse papel, ele recebe solicitações das aplicações que desejam realizar acesso a recursos e decide se vai conceder ou negar. Ao conceder solicitações de alocação, ele deve alocar com cuidado os recursos de modo que programas não interfiram uns com os outros

Por exemplo, não é uma boa ideia permitir que os programas tenham acesso sem restrição à memória uns dos outros. Se um programa malicioso escrever no espaço de memória de outro programa, o segundo programa pode produzir resultados diferentes. Pior ainda se o programa malicioso modificar a memória do sistema operacional, o que pode mudar o comportamento de todo o sistema

Quando pensamos no sistema operacional como um gerenciador de recursos, pensamos nele como a figura de autoridade do sistema

## Provedor de serviços

Quando falamos do ponto de vista do sistema como gerenciador de recursos, podemos pensar como sendo uma visão do ponto de vista do proprietário do sistema, que deseja se certificar que os recursos sejam usados de forma efetiva. Do outro lado, podemos pensar nas coisas com o ponto de vista da aplicação (ou do programador). Nessa perspectiva, queremos que o sistema operacional ofereça uma ótima coleção de serviços que facilite as tarefas da aplicação

Dessa forma, esperamos que a maior parte dos detalhes sobre acesso a dispositivos de E/S, alocação de memória, entre outros, seja cuidado pelo sistema operacional

## Máquina virtual

O último ponto de vista é o de **máquina virtual**. Essa perspectiva vem da observação do sistema operacional como uma interface entre a aplicação e o hardware. Podemos imaginar como um programa de uma aplicação olhando para baixo, para o sistema operacional e para o hardware. Nessa situação, a aplicação não sabe a diferença entre o computador com um hardware muito simples e poucos recursos e um computador com hardware muito complexo e muitos recursos se o sistema operacional fornecer os mesmos recursos nos dois casos

De forma simples, para a aplicação, a combinação entre hardware e sistema operacional é o "computador" onde ela será executada
