---
title: "HackThisSite - Desafio Realista 1 e 2"
classes: wide
header:
  teaser: /assets/images/Desafio-1-e-2/00.jpg
  overlay_image: /assets/images/Desafio-1-e-2/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateBlue
excerpt: "Write Up do desafio realista 1 e 2 do HackThisSite"
description: "Write Up do desafio realista 1 e 2 do HackThisSite"
categories:
  - Write Up
tags:
  - HackThisSite
  - SQL Injection
toc: false
toc_sticky: true
toc_label: "On This Blog"
toc_icon: "biohazard"
---
# Desafio Realista 1

![0](/assets/images/Desafio-1-e-2/00.png){: .align-center}

# Objetivo

O objetivo desse desafio é hackear o site para fazer a alteração no ranking e colocar a banda do seu amigo no topo da lista

"Ei cara, preciso de um grande favor seu. Lembra daquele site que te mostrei uma vez? Página de revisão da banda do tio Arnold? Bem, há muito tempo fiz uma aposta de $ 500 com um amigo que minha banda estaria no topo da lista até o final do ano. Bem, como você já sabe, dois membros da minha banda morreram em um terrível acidente de carro... mas esse idiota ainda insiste que a aposta está certa! Eu sei que você é bom com computadores e outras coisas, então eu queria saber, há alguma maneira de você hackear este site e colocar minha banda no topo da lista? Minha banda é Raging Inferno. Valeu mesmo, cara!"

Então vamos começar

# Coleta de informações

Acessando o site, podemos ver que a página possui um ranking de bandas, e que a banda do nosso amigo está em último lugar

![1](/assets/images/Desafio-1-e-2/01.png){: .align-center}

Analisando o código fonte da aplicação é possível identificar que o voto é enviado através do método get para a página "v.php" e nessa requisição são passados alguns valores

![2](/assets/images/Desafio-1-e-2/02.png){: .align-center}

Como pode ser visto no código fonte, é possível escolher uma note de 1 a 5 para a banda

# Exploração

Para entender melhor como a aplicação iria se comportar, realizei um voto na banda do nosso amigo, porém isso não causou nenhuma mudança na aplicação. Durante esse processo estava analisando as requisições e pude perceber que o voto foi enviado, mas apesar disso não fez diferença

![3](/assets/images/Desafio-1-e-2/03.png){: .align-center}

Imaginei que o valor atribuído para cada banda seria uma média dos votos recebidos, e um único voto não causaria nenhuma alteração no resultado

Para testar esse ponto, copiei a requisição feita para enviar o voto e alterei o valor do meu voto de 5 para 999999999

![4](/assets/images/Desafio-1-e-2/04.png){: .align-center}

E ao realizar a requisição, tive a confirmação da minha suspeita e assim consegui resolver o desafio

![5](/assets/images/Desafio-1-e-2/05.png){: .align-center}



# Desafio Realista 2

![6](/assets/images/Desafio-1-e-2/06.png){: .align-center}

# Objetivo

O objetivo desse desafio é conseguir obter acesso ao painel de administração do site

"Mensagem: Fui informado de que você possui habilidades de hacking bastante admiráveis. Bem, esse grupo de ódio racista está usando seu site para organizar uma reunião em massa de bastardos racistas ignorantes. Não podemos permitir que tal agressão preconceituosa aconteça. Se você puder obter acesso à página do administrador e postar mensagens na página principal, ficaremos eternamente gratos."

Então vamos começar

# Coleta de informações

Primeiramente vamos acessar a aplicação 

![7](/assets/images/Desafio-1-e-2/07.png){: .align-center}

Analisando o código fonte da aplicação, foi possível encontrar uma página para update

![8](/assets/images/Desafio-1-e-2/08.png){: .align-center}

Acessando essa página de update, nos deparamos com um painel de login

![9](/assets/images/Desafio-1-e-2/09.png){: .align-center}

Para entender um pouco mais sobre o comportamento da aplicação, enviei qualquer nome de usuário e senha, e ao fazer isso somos redirecionados para outra página

![10](/assets/images/Desafio-1-e-2/10.png){: .align-center}

# Exploração

Para tentar obter acesso a essa aplicação, tentei algumas credenciais padrões, como admin:admin, admin:123, entre outras, mas em nenhuma foi possível acessar a aplicação

Então por se tratar de um painel de autenticação, parti para o próximo teste e tentei realizar um ataque de SQL Injection 

![11](/assets/images/Desafio-1-e-2/11.png){: .align-center}

E dessa forma consegui burlar a autenticação e resolver o desafio

![12](/assets/images/Desafio-1-e-2/12.png){: .align-center}