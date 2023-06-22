---
title: "Entendendo o processo de compilação de um programa em C"
classes: wide
header:
  teaser: /assets/images/Entendendo-compilacao/00.jpg
  overlay_image: /assets/images/Entendendo-compilacao/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateGray
excerpt: "Breve artigo explicando como funciona o processo de compilação de um programa em C"
description: "Breve artigo explicando como funciona o processo de compilação de um programa em C"
categories:
  - Programação
tags:
  - Programação
  - C
  - Compiladores
toc: false
toc_sticky: true
toc_label: "Sumário"
toc_icon: "terminal"
---

# Processo de compilação

Ao produzir um executável a partir de um arquivo de código-fonte C, o processo de compilação na verdade passa por quatro estágios separados e cada um gera um novo arquivo:

- Pré-processamento - O pré-processador substitui todas as diretivas de pré-processamento no arquivo ".c" do código-fonte original pelo código da biblioteca que implementa essas diretivas. Por exemplo, a parte do código onde se encontram as diretivas ```#include <lib>``` será substituída pelo código da biblioteca que se deseja incluir. O arquivo gerado contendo as substituições tem formato de texto e geralmente possui uma extensão ".i".

- Tradução - O compilador traduz as instruções de alto nível do arquivo ".i" em instruções de linguagem Assembly de baixo nível. O arquivo gerado contendo a tradução tem formato de texto e normalmente tem uma extensão ".s".

- Assembling - O assembler converte as instruções de texto da linguagem Assembly criada no arquivo ".s" em código de máquina. O arquivo de objeto gerado contendo a conversão tem formato binário e geralmente tem uma extensão ".o".

- Vinculação (Linking) - O linker combina um ou mais arquivos de objeto binário ".o" em um único arquivo executável. O arquivo gerado tem formato binário e geralmente tem uma extensão de arquivo ".exe".

Estritamente falando, "compilação" descreve os três primeiros passos citados acima, que utilizam um único arquivo de código-fonte C e geram um único arquivo de objeto binário. Se em algum lugar do código-fonte do programa for encontrado erros de sintaxe, como um ponto e vírgula ausente ou um parêntese ausente, eles serão relatados pelo compilador e a compilação falhará.

O vinculador (linker), por outro lado, pode utilizar vários arquivos de objeto e gerar um único arquivo executável. Isso permite a criação de programas grandes a partir de arquivos de objetos modulares que podem conter funções reutilizáveis. Se o linker encontrar uma função com o mesmo nome, definida em mais de um arquivo de objeto, ele relatará um erro e o arquivo executável não será criado.

Normalmente, os arquivos temporários criados durante os estágios intermediários do processo de compilação são excluídos automaticamente, mas eles podem ser salvos incluindo a opção ```-save-temps``` no comando do compilador.

Vamos ver tudo isso acontecendo na prática. 

# Na prática

Para esse exemplo criei um código bem simples que somente mostra a mensagem "Hello World!" na tela.

![](/assets/images/Entendendo-compilacao/01.png){: .align-center}

Então compilei o programa usando a opção ```-save-temps``` e como pode ser visto na imagem abaixo, todos os 4 arquivos foram criados.

![](/assets/images/Entendendo-compilacao/02.png){: .align-center}

O primeiro arquivo criado foi o "hello.i", nele é possível ver que as diretivas de pré-processamento foram substituídas. Então, no local do ```#include <stdio.h>``` foi inserido todo o código presente na biblioteca stdio.

![](/assets/images/Entendendo-compilacao/03.png){: .align-center}

Agora que as substituições foram feitas, o próximo passo é realizar a tradução desse código para a linguagem assembly, então a partir do arquivo "hello.i" a tradução foi feita e o resultado foi salvo no arquivo "hello.s".

![](/assets/images/Entendendo-compilacao/04.png){: .align-center}

Com as instruções em assembly criadas, é a hora do assembler converter essas instruções para a linguagem de máquina, e o resultado é salvo no arquivo "hello.o". Esse arquivo possui o formato binário, então, não é possível visualizar o seu conteúdo. 

![](/assets/images/Entendendo-compilacao/05.png){: .align-center}

Agora que o arquivo objeto foi criado, basta o linker combinar o arquivo "hello.o" em um objeto executável, nesse exemplo o nome escolhido para esse executável foi "hello.exe".

Após todo esse processo, temos como resultado um programa funcional.

![](/assets/images/Entendendo-compilacao/06.png){: .align-center}
