---
title: "Minha experiência com a eMAPT"
classes: wide
header:
  teaser: /assets/images/eMAPT/00.jpg
  overlay_image: /assets/images/eMAPT/00.jpg
  overlay_filter: 0.5
ribbon: Firebrick
excerpt: "Nessa postagem falo um pouco sobre a minha experiência com a eMAPT, desde a preparação até a realização do exame"
description: "Nessa postagem falo um pouco sobre a minha experiência com a eMAPT, desde a preparação até a realização do exame"
categories:
  - Review
tags:
  - Certificação
  - INE
  - eLearnSecurity
  - eMAPT
  - Mobile
toc: true
toc_sticky: true
toc_label: "Sumário"
toc_icon: "terminal"
---

Nessa postagem, vou falar um pouco sobre a minha experiência com a eMAPT, desde a preparação até a realização da prova, e passar algumas dicas que acho interessante pra quem pensa em tirar essa certificação

# O curso

A algum tempo a INE comprou a eLearnSecurity e todos os treinamentos e certs passaram para a plataforma da INE, os cursos não são vendidos separados mas somente por assinatura, então você faz a assinatura na plataforma e pode acessar todos os cursos por um determinado período de tempo. Já as certificações podem ser compradas separadamente e tem o valor de 400 doláres cada

Durante a Black Friday, eu aproveitei a promoção e consegui a assinatura de 1 ano da INE com uma certificação por 499 dólares ( mas na hora de pagar só descontou 350 🤷 ) e tudo deu aproximadamente uns R$1.800

O path de estudos para essa prova é dividido em 2 cursos: Pentest em apps Android e Pentest em apps IOS. Para a prova só é cobrado o teste em app Android, então se você quiser, pode ignorar a parte de IOS

Durante o curso são abordados temas como: Arquitetura do Android, Engenharia Reversa, Básico sobre desenvolvimento Android, Análise de código estática e dinâmica, entre outros...

Sinceramente eu achei o curso bem desatualizado, as vulnerabilidades abordadas durante o curso são bem antigas e são muito difíceis de serem encontradas hoje em dia. Durante o curso também não é ensinado nada sobre proteções e quebra de proteções. Por esses motivos eu achei o curso bem distante da realidade 

Talvez, se você está começando os seus estudos na área de mobile seja uma boa opção fazer o curso para pegar algumas bases que vão ajudar durante o seu aprendizado, mas se você já tem uma noção mínima de como um teste mobile funciona, acredito que o curso não vai valer a pena

# A prova

Ao iniciar a prova você recebe as regras de engajamento e qual são os objetivos do teste. Ao todo você tem 7 dias para fazer o teste e entregar o que é solicitado

Você recebe 2 apps para testar e o objetivo é criar um exploit (aplicativo) que vai explorar esses dois apps que você recebeu. O seu app tem que explorar esses dois aplicativos ao mesmo tempo e ele não pode ter nada hardcoded, o seu exploit tem que ser capaz de explorar outros apps similares em outros dispositivos. Para validar isso eles te enviam outros 2 apps (ao todo são 4), esses aplicativos são idênticos aos 2 primeiros, porém possuem informações diferentes, com isso você consegue testar se de fato o seu exploit está portável e funcionando em qualquer cenário

As vulnerabilidades são relativamente fáceis de se encontrar, e com pouco tempo você vai conseguir identificar o problema, além disso os apps não possuem nenhuma proteção e nem ofuscação de código. Para mim a parte mais difícil foi o desenvolvimento do app porque eu não tenho muita experiencia com desenvolvimento Android.

Mesmo assim não foi um bicho de sete cabeças e com algumas pesquisas no google você consegue encontrar referências para implementar o que você precisa

Ao final do exame você só precisa enviar o seu exploit (.apk) e o código fonte, não sendo necessário o envio de relatório

# Vale a pena?

Mas afinal, vale a pena fazer a prova? Bom, acredito que depende. Depende do seu objetivo ao fazer a prova

Se você pensa em fazer a prova para aprender e se preparar para trabalhar com testes mobile, eu acredito que não vale a pena. Falo isso porque a prova está bem desatualizada, as vulnerabilidades são fáceis de encontrar e explorar e não possuí nenhuma proteção

Se a sua intenção é aprender, existem alternativas no mercado que irão te preparar melhor para o dia a dia

Por outro lado, se você tem a intenção de melhorar o seu currículo, talvez seja uma boa opção, pois apesar de tudo o que falei, a eMAPT continua sendo uma das provas mais conhecidas quando se fala de mobile e normalmente é bem vista pelo RH

Outro caso, como já falei anteriormente, se você está começando os seus estudos agora, esse talvez seja um bom ponta pé inicial

# Dicas

A parte mais difícil da prova é o desenvolvimento do exploit, na minha opinião, isso porque eu não tenho muita familiaridade com desenvolvimento para Android, pra quem já tem experiência com desenvolvimento Android essa prova vai ser muito tranquila

Então vou deixar aqui alguns links que usei de consulta e que me ajudaram durante a prova, talvez ajude você também

<a href="https://infosecwriteups.com/exploiting-android-vulnerabilities-with-malicious-third-party-apps-featuring-oversecured-apk-adea3241ce49" target="_blank">Explorando Vulnerabilidades Android com aplicativos maliciosos</a><br>
<a href="https://book.hacktricks.xyz/mobile-pentesting/android-app-pentesting/drozer-tutorial/exploiting-content-providers" target="_blank">Explorando Content Providers</a><br>
<a href="https://developer.android.com/guide/topics/providers/content-providers" target="_blank">Content Providers</a><br>
