---
date: 2023-04-22 23:48:05
layout: post
title: HackThisSite - Desafio Realista 3 e 4
subtitle: 
description: >-
  Write Up do desafio realista 3 e 4 do HackThisSite.
image: >-
  /assets/img/Desafio-3-e-4/00.jpg
optimized_image: >-
  /assets/img/Desafio-3-e-4/00.jpg
category: Write Up
tags:
  - HackThisSite
  - SQL Injection
  - File Upload
author: squ4nch
paginate: false
---

# Desafio 3

![](/assets/img/Desafio-3-e-4/00.png){: .align-center}

## Objetivo

Basicamente o objetivo desse desafio é restaurar um site que foi hackeado para o seu conteúdo original

"Mensagem: Eu administro este site onde as pessoas podem ler e enviar poesia relacionada à paz. Estou fazendo isso de boa vontade para com os outros, e não vejo por que faria inimigos com isso, mas algum idiota de verdade invadiu meu site postando um monte de propaganda agressiva e ignorante na primeira página. E eu fiz esse site há um tempo atrás, e não tenho mais acesso a ele. Você acha que pode invadir e mudá-lo de volta? Por favor? Ah, e pontos de bônus se você me enviar uma mensagem com o nome do bastardo que fez isso!"

## Coleta de informações

Acessando o site, podemos ver a página hackeada
  
![](/assets/img/Desafio-3-e-4/01.png){: .align-center}

Olhando o código fonte da aplicação é possível encontrar um comentário do invasor
  
![](/assets/img/Desafio-3-e-4/02.png){: .align-center}

Basicamente o invasor diz que não destruiu o site original, ele simplesmente copiou o site original para o arquivo oldindex.html e criou um novo, vamos dar uma olhada nesse site original então
  
![](/assets/img/Desafio-3-e-4/03.png){: .align-center}

No site original, é possível encontrar dois links: um para ler os poemas existentes no site e outro para enviar o seu próprio poema

A página de leitura dos poemas apresenta uma lista com todos os poemas
  
![](/assets/img/Desafio-3-e-4/04.png){: .align-center}

E o poema escolhido para leitura é passado através de um parâmetro na URL
  
![](/assets/img/Desafio-3-e-4/05.png){: .align-center}

## Exploração

Como é possível perceber, a aplicação utiliza um código php para ler e mostrar o conteúdo de um outro arquivo, então, minha primeira tentativa foi de ler algum arquivo do sistema, e tentar explorar um LFI, porém isso não era permitido
  
![](/assets/img/Desafio-3-e-4/06.png){: .align-center}

Para entender como o envio de poemas funcionava, enviei um conteúdo qualquer para entender o processo
  
![](/assets/img/Desafio-3-e-4/07.png){: .align-center}

Após o envio somos redirecionados para uma página de sucesso
  
![](/assets/img/Desafio-3-e-4/08.png){: .align-center}

Como o próprio site nos avisa, o poema enviado fica disponível online imediatamente, porém ele não é listado para leitura, mesmo assim tentei passar o nome do poema enviado no parâmetro para leitura e também não foi possível encontrar o arquivo

Analisando os poemas disponíveis e a forma de envio, imaginei que muito provavelmente o nome do poema enviado é o nome do arquivo salvo no sistema, tendo em vista que é através do nome do poema que o código php mostra o conteúdo, então quis testar para saber se era possível sobrescrever algum arquivo que já está presente no servidor passando o nome dele como título, e minha primeira tentativa foi de alterar o conteúdo do index.html, pois qualquer alteração seria visível para mim

Dessa forma enviei um novo poema, mas dessa vez passando o caminho do index.html como nome
  
![](/assets/img/Desafio-3-e-4/09.png){: .align-center}

E então recebi a seguinte mensagem:
  
![](/assets/img/Desafio-3-e-4/10.png){: .align-center}

Basicamente a mensagem diz que a minha ideia estava certa, mas que o objetivo era restaurar a página inicial do site, sabendo disso então copiei o conteúdo HTML do oldindex.html e enviei como poema
  
![](/assets/img/Desafio-3-e-4/11.png){: .align-center}

Dessa forma, foi possível concluir o desafio
  
![](/assets/img/Desafio-3-e-4/12.png){: .align-center}

#  Desafio 4

![](/assets/img/Desafio-3-e-4/13.png){: .align-center}

## Objetivo

"Mensagem: Olá, fui indicado a você por um amigo que diz que você sabe como invadir computadores e sites - bem, eu queria saber se você poderia me ajudar aqui. Existe uma loja local que está matando centenas de animais por dia exclusivamente com o propósito de vender jaquetas e bolsas de pele! Eu fui ao site deles e eles têm uma lista de e-mail para seus clientes. Eu queria saber se você poderia de alguma forma hackear e me enviar todos os endereços de e-mail dessa lista. Quero enviar-lhes uma mensagem informando-os sobre o assassinato que estão vestindo. Basta responder a esta mensagem com uma lista dos endereços de e-mail. Por favor? O site deles é http://www.hackthissite.org/missions/realistic/4/. Muito obrigado!!"

## Coleta de informações

Acessando o site encontramos um campo para realizar o cadastro do e-mail
  
![](/assets/img/Desafio-3-e-4/14.png){: .align-center}

Também é possível encontrar 2 links que direcionam para o catálogo de produtos vendidos
  
![](/assets/img/Desafio-3-e-4/15.png){: .align-center}

A aplicação passa o número da categoria que deve ser mostrada como um parâmetro na URL, então, enviei uma letra para ver como a aplicação se comportava e ela não retornou nada
  
![](/assets/img/Desafio-3-e-4/16.png){: .align-center}

Nesse momento, acreditei que a aplicação poderia estar vulnerável a SQL Injection, então realizei o primeiro teste, e passei junto com o parâmetro uma condição
  
![](/assets/img/Desafio-3-e-4/17.png){: .align-center}

A aplicação trouxe o resultado normalmente, então fiz o segundo teste passando uma condição falsa
  
![](/assets/img/Desafio-3-e-4/18.png){: .align-center}

E dessa vez, a aplicação não mostrou nenhum resultado, desta forma, tive a confirmação que a aplicação está vulnerável a SQL Injection

## Exploração

Sabendo que a aplicação está vulnerável, o primeiro passo foi descobrir a quantidade de colunas que a tabela possui, e para isso utilizei o "order by"
  
![](/assets/img/Desafio-3-e-4/19.png){: .align-center}

Sabendo que a tabela possui 4 colunas, o segundo passo foi tentar refletir algum desses campos no conteúdo do site para que eu pudesse executar algum comando ou fazer algo nesse sentido. Para isso eu utilizei o "union all" e conseguir refletir os campos passados
  
![](/assets/img/Desafio-3-e-4/20.png){: .align-center}

Agora que consegui refletir o conteúdo, tentei descobrir a versão do banco de dados para saber quais comandos poderia usar, então testei o comando para mostrar a versão das linguagens mais conhecidas e nenhum deles trouxe resultado. Não sabendo qual banco de dados está rodando na aplicação fui realizando alguns testes meio que no "chute"

Como o objetivo é descobrir uma lista de e-mails, parti do pressuposto que existe uma tabela de nome e-mail onde esses valores são armazenados e tentei unir a seleção pra mostrar o conteúdo dessa tabela também e deu certo
  
![](/assets/img/Desafio-3-e-4/21.png){: .align-center}

A aplicação não mostrou o e-mail, mas trouxe uma "lista" mostrando que de fato existe uma tabela de nome e-mail, para conseguir ver o conteúdo dessas colunas é necessário informar o nome da coluna que eu quero visualizar. Novamente então dei um "chute" e parti do pressuposto que a coluna contendo os e-mails se chamaria e-mail e dessa vez consegui ver o seu conteúdo
  
![](/assets/img/Desafio-3-e-4/22.png){: .align-center}

Agora que tenho acesso a lista de e-mails, bastou enviar uma mensagem para a pessoa que solicitou ajuda
  
![](/assets/img/Desafio-3-e-4/23.png){: .align-center}

E assim, concluir o desafio
  
![](/assets/img/Desafio-3-e-4/24.png){: .align-center}