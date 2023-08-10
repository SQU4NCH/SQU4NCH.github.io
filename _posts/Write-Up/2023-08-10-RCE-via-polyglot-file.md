---
title: "Remote Code Execution via Polyglot Web Shell Upload"
classes: wide
header:
  teaser: /assets/images/RCE-via-polyglot-file/00.jpg
  overlay_image: /assets/images/RCE-via-polyglot-file/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateBlue
excerpt: "Write Up do lab 'Remote Code Execution via Polyglot Web Shell Upload' da PortSwigger"
description: "Write Up do lab 'Remote Code Execution via Polyglot Web Shell Upload' da PortSwigger"
categories:
  - Write Up
tags:
  - PortSwigger
  - Web Shell
  - File Upload
  - Arquivos Poliglotas

toc: false
toc_sticky: true
toc_label: "On This Blog"
toc_icon: "biohazard"
---

![](/assets/images/RCE-via-polyglot-file/01.png){: .align-center}

# Objetivo

Basicamente o objetivo desse laboratório é conseguir fazer o upload de uma web shell em PHP para ler o conteúdo do arquivo "/home/carlos/secret" 

# Execução

Acessando a conta do usuário com as credenciais passadas, é possível atualizar o Email e enviar uma imagem de perfil

![](/assets/images/RCE-via-polyglot-file/02.png){: .align-center}

Apesar da descrição do lab falar que a aplicação só aceita arquivos que são genuinamente imagens, resolvi fazer uma validação para confirmar que o filtro de arquivos estava funcionando, para isso, tentei enviar um arquivo PHP e como esperado a aplicação retornou um erro

![](/assets/images/RCE-via-polyglot-file/03.png){: .align-center}

Como só podemos enviar uma imagem e ao mesmo tempo precisamos executar um código em PHP é preciso criar um arquivo poliglota que seja ao mesmo tempo esses dois tipos de arquivo. Para isso, utilizei a ferramenta "exiftool" para adicionar um novo campo nos metadados do arquivo de imagem

![](/assets/images/RCE-via-polyglot-file/04.png){: .align-center}

Como é possível inserir metadados, utilizei esse campo para passar um código em PHP que realizasse a leitura do arquivo contendo o secret

```php
<?php echo '## secret => ' . file_get_contents('/home/carlos/secret') . ' ##' ?>
```

![](/assets/images/RCE-via-polyglot-file/05.png){: .align-center}

Para que o código inserido seja executado, é necessário alterar a extensão do arquivo para .php, para que o servidor interprete esse arquivo como um PHP e execute o código passado da maneira correta. 

![](/assets/images/RCE-via-polyglot-file/06.png){: .align-center}

Apesar de realizar a alteração da extensão do arquivo para .php ele continua sendo reconhecido como um arquivo PNG válido, por causa de sua assinatura. Por ser um arquivo PNG e por conter um código que será executado em PHP esse arquivo é considerado um arquivo poliglota

Então, voltei a aplicação e enviei esse novo arquivo como imagem de perfil, dessa vez o arquivo foi enviado sem problemas

![](/assets/images/RCE-via-polyglot-file/07.png){: .align-center}

Voltando a conta do usuário, é possível ver que a imagem foi carregada

![](/assets/images/RCE-via-polyglot-file/08.png){: .align-center}

Agora, basta abrir a imagem em uma nova aba para ver o seu conteúdo

![](/assets/images/RCE-via-polyglot-file/09.png){: .align-center}

Como esperado, o código em PHP foi executado e como resposta ele mostrou o conteúdo do arquivo secret que estava na pasta do usuário carlos

Agora basta submeter a flag e resolver o lab

![](/assets/images/RCE-via-polyglot-file/10.png){: .align-center}