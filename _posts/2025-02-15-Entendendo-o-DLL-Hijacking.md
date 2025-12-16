---
date: 2025-02-15 23:48:05
layout: post
title: Entendendo o DLL Hijacking
subtitle: 
description: >-
  Neste artigo, vamos explorar o que é DLL Hijacking, como funciona a busca de DLLs pelo sistema operacional e um exemplo prático usando o Teams.
image: >-
  /assets/img/DLL/00.jpg
optimized_image: >-
  /assets/img/DLL/00.jpg
category: Mal Dev
tags:
  - Desenvolvimento de Malwares
author: squ4nch
paginate: false
---


Neste artigo, vamos explorar o que é DLL Hijacking, como funciona a busca de bibliotecas de vínculo dinâmico (DLLs) pelo sistema operacional e um exemplo prático usando o Microsoft Teams. Além disso, discutiremos medidas essenciais que desenvolvedores podem adotar para proteger suas aplicações contra esse tipo de ataque.
## O que é uma DLL

Antes de falarmos sobre DLL Hijacking precisamos entender alguns conceitos base que são fundamentais para o entendimento do ataque como um todo. Começando com o principal: o que é uma DLL?

Segundo a documentação da Microsoft:

> "Uma _dynamic-link library_ (DLL) é um modulo que contém funções e dados que podem ser usados por outros módulos (aplicação ou DLL)."

Uma DLL pode definir dois tipos de funções: exportadas e internas. As funções exportadas podem ser usadas pela própria DLL e por outros módulos. As funções internas podem ser usadas somente pela própria DLL onde elas foram definidas. Apesar da DLL poder exportar dados, esses dados geralmente são usados somente pelas suas próprias funções. Entretanto, não há nada que impeça outros módulos de lerem e escreverem nesse endereço.
## Ordem de busca de um DLL

Quando um executável é carregado na memória, todas as bibliotecas que ele usa também são carregadas.

O Windows vai buscar essas bibliotecas seguindo uma ordem específica. De forma resumida essa ordem é:

1. DLLs conhecidas (kernel32.dll, user32.dll, etc)
2. Pasta onde a aplicação foi carregada
3. Pasta do sistema (%SystemRoot%\system32)

*A documentação do Windows descreve de forma mais detalhada essa sequência, mas para o nosso propósito essa forma resumida já é suficiente.*
## DLL Hijacking

Agora que já sabemos o que é uma DLL e como o Windows procura uma DLL no sistema, vamos imaginar a seguinte situação: você vai rodar um executável e esse executável usa uma DLL que está localizada na pasta system32. Então você cria uma DLL com o mesmo nome da DLL que o executável utiliza e salva ela na pasta onde o programa está localizado. O que você acha que vai acontecer quando o programa for iniciado? Se você pensou que o programa vai carregar a sua DLL no lugar da DLL original, você está certo.

O  DLL Hijacking consiste em se aproveitar da ordem de busca para inserir uma DLL maliciosa e conseguir execução de código. 
## Exemplo prático usando o Teams

Para esse teste eu vou usar o Process Monitor para monitorar os processos da minha máquina. Para direcionar a nossa busca, vou utilizar alguns filtros.

Como o nosso alvo é o Teams, então filtrei o resultado para mostrar somente os processos com o nome "Teams.exe". A ideia é identificar as DLLs que o programa está buscando, então também filtrei para mostrar os paths que terminam com "dll". Por último, utilizei um filtro para me mostrar somente os processos com o resultado "NAME NOT FOUND", isso serve para identificar as DLLs que o programa tentou carregar e por algum motivo não encontrou, seguindo a sequência que a gente já viu anteriormente. 

![](/assets/img/DLL/01.png){: .align-center}

Com os filtros aplicados, basta executar o Teams e ver o resultado no procmon.

![](/assets/img/DLL/02.png){: .align-center}

Como podemos ver na imagem acima, o Teams tenta carregar algumas DLLs que não estão presentes na pasta onde a aplicação foi carregada. Agora temos todas as informações necessárias para realizar o ataque.

Para esse exemplo, vou utilizar a "DWrite.dll", que é uma DLL bem conhecida. Fui até a pasta da aplicação "C:\Users\teodo\AppData\Local\Microsoft\Teams\current\" (no meu caso) e salvei a minha DLL com o nome "DWrite.dll". Essa nova DLL abre a calculadora e não executa mais nada, somente para POC.

*Se quiser reproduzir esse teste, a DLL utilizada pode ser baixada em: https://squ4nch.github.io/Dll1.dll*

![](/assets/img/DLL/03.png){: .align-center}

Com tudo pronto, basta abrir o Teams novamente e a DLL criada deve ser executada.

![](/assets/img/DLL/DLLHijacking.gif){: .align-center}
## Como se proteger 

A própria documentação da Microsoft trás algumas diretrizes que os desenvolvedores podem seguir para proteger as aplicações contra esse tipo de ataque, algumas delas são:

- Sempre que possível, especifique o caminho completo ao usar as funções LoadLibrary, LoadLibraryEx, CreateProcess ou ShellExecute.
- Considere usar o redirecionamento de DLL ou um manifesto para garantir que seu aplicativo use a DLL correta.
- Ao usar a ordem de pesquisa padrão, verifique se o modo de pesquisa de DLL seguro está habilitado. Isso coloca o diretório atual do usuário posteriormente na ordem de pesquisa, aumentando as chances de o Windows encontrar uma cópia legítima da DLL antes de uma cópia mal-intencionada.
- Considere remover o diretório atual do caminho de pesquisa padrão chamando SetDllDirectory com uma cadeia de caracteres vazia ("").

Como falei anteriormente, essas diretrizes estão presentes na documentação da Microsoft e de forma muito mais completa, então, recomendo a leitura.
## Conclusão

O DLL Hijacking é um ataque que explora vulnerabilidades na forma como o Windows localiza e carrega DLLs. Ao manipular a ordem de busca das DLLs, um atacante pode substituir uma biblioteca original por uma versão maliciosa, comprometendo a segurança de aplicações e sistemas.

Essa é uma técnica relevante tanto para as operações de red team quanto para as atividades de blue team. Podendo ser executada de forma ofensiva durante operações para conseguir a execução de comandos e também de forma defensiva para aprimorar a detecção de atividades maliciosas e implementar medidas de segurança eficazes.
## Referências

- WHITE, Steven; SINGH, Gurkirat; SCHOFIELD, McLean; COULTER, David; JACOBS, Mike; SATRAN, Michael; NEVER, Friedrich. Dynamic-link library search order. In.: Microsoft Learn. 08 de fevereiro de 2023. Disponível em: https://learn.microsoft.com/en-us/windows/win32/dlls/dynamic-link-library-search-order. Acesso em 13 de fevereiro de 2025.
- WHITE, Steven; SHARKEY, Kent; COULTER, David; JACOBS, Mike; SATRAN, Michael. Dynamic-Link Library Security. In.: Microsoft Learn. 07 de janeiro de 2021. Disponível em: https://learn.microsoft.com/en-us/windows/win32/dlls/dynamic-link-library-security. Acesso em 13 de fevereiro de 2025.
- WHITE, Steven; SANTOS, Brittany; SHARKEY, Kent; COULTER, David; SATRAN, Michael. Dynamic-Link Libraries (Dynamic-Link Libraries). In.: Microsoft Learn. 31 de maio de 2022. Disponível em: https://learn.microsoft.com/en-us/windows/win32/dlls/dynamic-link-libraries. Acesso em 13 de fevereiro de 2025.