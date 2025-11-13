---
title: 'Git Dis-amend: O Perigo de "Reescrever" a História'
classes: wide
header:
  teaser: /assets/images/Git-Commit/00.jpg
  overlay_image: /assets/images/Git-Commit/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateGray
excerpt: "Nesse artigo eu mostro possíveis formas de se abusar do comportamento do comando `git commit --amend`"
description: "Nesse artigo eu mostro possíveis formas de se abusar do comportamento do comando `git commit --amend`"
categories:
  - Research
tags:
  - Git
  - GitHub
  - Artigo  
toc: true
toc_sticky: true
toc_label: "Sumário"
toc_icon: "terminal"
---

# Introdução

O Git é o principal sistema de controle de versão usado atualmente e possui uma lista extensa de comandos para os mais diversos propósitos. Um desses comandos, o `git commit --amend`, é normalmente utilizado para substituir o commit mais recente, permitindo corrigir erros ou adicionar alterações de última hora.

No entanto, o `git commit --amend` não "reescreve" de fato o commit anterior. Devido à natureza imutável dos commits, o que realmente acontece é a criação de um novo commit, fazendo com que o Git pare de referenciar o commit antigo em seu lugar.

Mas o que acontece com esse commit antigo? A resposta é que ele continua existindo. Embora não seja mais mostrado no histórico da branch, ele permanece acessível se alguém souber seu hash.

Este artigo mostra como esse comportamento, aparentemente inofensivo, pode ser abusado para fins maliciosos. 

# Como o Git Funciona

O Git é um sistema de controle de versão de código aberto, projetado para rastrear de forma inteligente as alterações nos arquivos, sendo extremamente útil quando múltiplos indivíduos estão alterando os mesmos arquivos simultaneamente. 

Ele permite que vários colaboradores criem um ramo (branch) a partir de uma cópia do projeto principal, permitindo que editem os arquivos de forma independente e segura. Ao fazer alterações (commits) em arquivos armazenados em um repositório Git, o sistema começa automaticamente a gerenciar e rastrear essas mudanças. 

Dessa forma, os usuários podem puxar (pull) os arquivos mais recentes do repositório remoto e enviar (push) suas mudanças. O Git então, é responsável por fundir (merge) de forma inteligente esse fluxo de alterações de volta ao repositório principal, mantendo todos trabalhando na versão mais atualizada.

## Diferenças Entre Git e GitHub

Git é o sistema de controle de versão em si, ou seja, ele é o software que faz o rastreio e gerencia de forma inteligente as alterações nos arquivos. Ele é a tecnologia sobre o qual o GitHub é construído.

Já o GitHub é uma plataforma baseada em nuvem que oferece um lugar para armazenar os projetos, atuando como um repositório remoto. Na prática, quando você envia arquivos para o GitHub, eles são armazenados em repositórios Git, o que faz com que o Git automaticamente comece a monitorar as mudanças.

De forma geral, o Git é o software que lida com o histórico e a fusão de alterações, enquanto o GitHub é a ferramenta de hospedagem e colaboração que utiliza o Git para permitir que as pessoas trabalhem juntas.

## git commit --amend

O Git possui uma lista extensa de comandos, para os mais diversos propósitos. No contexto desse artigo, gostaria de destacar um deles, o `git commit --amend`.

O comando `git commit --amend` serve para substituir o commit mais recente do branch atual. Em vez de criar um novo commit adicional, ele o "reescreve", colocando as alterações realizadas (como se tivessem sido adicionadas com `git add` ou `git rm`). O Git então prepara a nova árvore de arquivos (incluindo o conteúdo do índice) e utiliza a mensagem do commit original como ponto de partida, caso uma nova mensagem não tenha sido especificada com opções como `-m` ou `-F`. 

O novo commit resultante mantém os mesmos pais e autor do commit anterior, se a opção `--reset-author` não for usada. É importante notar que este comando reescreve o histórico de commits, então, se você modificar um commit que já foi publicado, você deve entender as implicações dessa reescrita de histórico.

Apesar dessa alteração no histórico de commits, o comando `git commit --amend` não "reescreve" de fato o último commit, isso porque os commits possuem uma característica importante: eles são imutáveis. Na prática o que acontece então, é que um novo commit é criado, e então o Git para de referenciar o commit antigo e passa a referenciar o novo commit no seu lugar.

# Utilizando o Comando "amend" para Fins Maliciosos

Devido a essa característica que citei acima, eu comecei a me perguntar: o que acontece com o commit antigo? Ele fica no limbo? É possível acessar o seu conteúdo?

E a resposta é: sim! Ele continua existindo, é possível acessar o seu conteúdo (se você souber o nome do commit) e ele fica no "limbo", pois apesar de continuar existindo, ele não é mostrado em nenhum lugar do repositório.

Então, veio a próxima pergunta: Será que eu consigo usar isso de alguma forma maliciosa?

Bom, eu pensei em algumas opções e ideias de ataques abusando desse comportamento, vamos passar por cada uma delas.

## Monitorando para Recuperar Informações Sensíveis

Na prática, uma pessoa só vai usar o comando `git commit --amend` quando ela cometer algum erro, afinal, não é necessário "reescrever" o commit se ele estiver certo. Esse erro pode ser algo simple ou pode ser a exposição de informações sensíveis, como por exemplo, credencias ou chaves de API.

Então, uma ideia é monitorar um repositório específico para acompanhar os seus commit de forma a identificar quando algum commit foi alterado e então acessar ele para tentar encontrar essas informações.

Como uma prova de conceito, eu simulei um commit acidental para o meu repositório pessoal contendo uma mensagem secreta.

Como se pode ver, o último commit do repositório é o "544b1f6...".

![](/assets/images/Git-Commit/01.png){: .align-center}

Acessando o conteúdo co commit, é possível ver a mensagem "secret stuff".

![](/assets/images/Git-Commit/02.png){: .align-center}

Então, eu usei um script para "monitorar" esse repositório. Ele basicamente pega o conteúdo da página de commits e salva em um arquivo para ser comparado no futuro.

![](/assets/images/Git-Commit/03.png){: .align-center}

Após isso, eu usei o `git commit --amend` para criar um novo commit para substituir o último com a mensagem secreta.

![](/assets/images/Git-Commit/04.png){: .align-center}

Acessando a página de commits agora, é possível ver que o último commit é o "04cabeb.." e o "544b1f6..." simplesmente desapareceu.

![](/assets/images/Git-Commit/05.png){: .align-center}

Nesse novo commit, a mensagem "secret stuff" não existe, então o "problema" foi corrigido.

![](/assets/images/Git-Commit/06.png){: .align-center}

Usando novamente o script, é possível comparar o conteúdo atual dos commits com o salvo anteriormente e é possível identificar uma mudança no último commit.

![](/assets/images/Git-Commit/07.png){: .align-center}

Nas informações, nós temos a hash e a URL de cada commit, apesar do commit 544b1f6... ter "desaparecido", ele continua existindo e simplesmente não está sendo referenciado pelo git.

Acessando a URL dele, é possível recuperar novamente a mensagem secreta.

![](/assets/images/Git-Commit/08.png){: .align-center}

Na página do commit é mostrada uma mensagem informando que esse commit não faz mais parte de nenhuma branch no repositório, porém, mesmo assim é possível ver o seu conteúdo.

Esta é apenas uma POC simples, e a ideia principal aqui é mostrar uma possível forma de abuso desse comportamento. Em uma abordagem realista, ele poderia monitorar alvos específicos diariamente (ou a cada hora) e identificar quando eles vazam informações sensíveis em seus repositórios.

Limitações: você precisa ter acesso ao repositório, então, ele vai funcionar com repositórios públicos, e claro, com repositórios privados que você tem acesso.

## Exfiltração de Dados

Outra possível forma de abusar desse comportamento, e seguindo a mesma ideia, é usar ele para exfiltração de dados. 

Pensando no caso de um insider que deseja exfiltrar dados de uma empresa, porém essa empresa possui controles fortes quanto a upload de arquivos para sites externos. Uma alternativa seria realizar um commit com essas informações para um repositório que a empresa permite acesso e depois usar o `git commit --amend` para "esconder" essas informações.

Dessa forma, basta saber a URL do commit para acessar as informações vazadas.

## Escondendo Código Malicioso em Bons Repositórios

Uma terceira ideia, seria esconder códigos maliciosos dentro de repositórios conhecidos. 

A ideia de novo é muito parecida, o agente malicioso, poderia fazer um commit contendo um código malicioso para o repositório e após isso usar o `git commit --amend` para "esconder" esse código. Dessa forma, o código malicioso vai estar dentro do repositório, porém não será referenciado em nenhum lugar.

Para criar uma POC, eu segui o mesmo exemplo do primeiro caso, porém, passando dois comandos: `whoami` e `pwd`.

![](/assets/images/Git-Commit/09.png){: .align-center}

Da mesma forma, um novo commit foi gerado utilizando o `--amend` para "esconder" esse conteúdo "malicioso".

Sabendo a URL do commit, podemos agora utilizar o PowerShell para realizar a requisição e executar o comando diretamente no terminal.

![](/assets/images/Git-Commit/10.png){: .align-center}

Novamente, a ideia aqui é apenas mostrar uma POC simples, provando que é possível abusar dessa funcionalidade para fins maliciosos. Em um cenário real, a criatividade não tem limite.

# Conclusão

O comando `git commit --amend` é, sem dúvida, uma ferramenta útil no dia a dia, permitindo que desenvolvedores corrijam rapidamente o commit mais recente. No entanto, como tentei mostrar neste artigo, o fato de que commits são imutáveis e que o `amend` na verdade cria um novo commit, parando de referenciar o antigo gera um comportamento que pode ser explorado para fins maliciosos.

Nesse artigo, eu trouxe três exemplos simples, mostrando possíveis cenários de exploração. Embora os exemplos apresentados tenham sido simples, eles provam que é possível explorar essa funcionalidade. Em um cenário real e com mais criatividade, o potencial de abuso dessa característica não tem limites.

# Referências

(1) https://git-scm.com/docs/

(2) https://docs.github.com/en/get-started/start-your-journey/about-github-and-git