---
title: "HTTP Request Smuggling"
classes: wide
header:
  teaser: /assets/images/HTTP-Request-Smuggling/00.jpg
  overlay_image: /assets/images/HTTP-Request-Smuggling/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateGray
excerpt: "Artigo explicando como o HTTP Request Smuggling acontece e como explora-lo"
description: "Artigo explicando como o HTTP Request Smuggling acontece e como explora-lo"
categories:
  - Web Hacking
tags:
  - HTTP Request Smiggling
  - Web
  - Artigo
toc: true
toc_sticky: true
toc_label: "Sumário"
toc_icon: "terminal"
---

# Introdução

A primeira vez que o HTTP Request Smuggling foi documentado foi em 2005 pela Watchfire mas devido a fama de ser um ataque muito complexo e pelo fato de não conseguir mensurar os seus danos, essa falha acabou sendo deixada de lado pelos pesquisadores. Porém, com o passar dos anos e com a evolução dos aplicativos Web, novos vetores de ataque e novas maneiras de exploração acabaram surgindo.

O HTTP Desync está diretamente relacionado com o uso de Load Balancer / Reverse Proxy e acontece quando ocorre uma dessincronização entre o back-end e esses serviços.

Nesse artigo eu vou mostrar para você como esse ataque funciona, como podemos identificar e explorar essa falha e como podemos proteger nossa aplicação para que ela não fique vulnerável a esse tipo de ataque.

  

# O que é e como funciona o HTTP Request Smuggling

Antes de falar sobre o HTTP Request Smuggling, precisamos entender como funciona uma conexão HTTP. Desde a sua versão 1.1 o HTTP tem suporte para múltiplas requisições em uma única conexão, o que não traz nenhum problema. Porém, as aplicações Web modernas implementam várias camadas (como por exemplo um servidor com Load Balancer) e essas camadas se comunicam através do protocolo HTTP, o que permite que as requisições de vários usuários sejam encaminhadas utilizando a mesma conexão.

  
![](/assets/images/HTTP-Request-Smuggling/01.png){: .align-center}

Existem duas maneiras de definir o tamanho do corpo de uma requisição no HTTP: usando o header Content-Length ou usando outro header chamado Transfer-Encoding.

O Content-Length define o tamanho total do corpo da requisição, em bytes, e envia todo o corpo de uma vez. Já o Transfer-Encoding, com a opção chunked, divide o corpo da requisição em vários fragmentos e, no começo de cada fragmento, você precisa adicionar o tamanho do fragmento atual em formato hexadecimal seguido por '\\r\\n' e o fragmento em si, seguido por outro '\\r\\n'.

  
![](/assets/images/HTTP-Request-Smuggling/02.png){: .align-center}

Mas, o que aconteceria se colocassemos as duas opções no header da requisição? Segundo a RFC 2616 na seção 4.4 Message Length:

_“Se uma mensagem for recebida com um campo de cabeçalho Transfer-Encoding e um campo de cabeçalho Content-Length, o último DEVE ser ignorado.”_

A RFC é bem clara no que deve ser feito, contudo, nem todo mundo segue o que está escrito nela e quando uma aplicação não respeita o que está descrito na RFC o HTTP Request Smuggling se torna possível.

É muito comum hoje em dia encontrarmos aplicações utilizando Load Balancer ou Reverse Proxy. E se o Proxy interpretar minha requisição diferente do back-end, porque um dos dois não está seguindo o RFC, o que pode acontecer?

Vamos imaginar que um usuário fez uma requisição para um determinado site e no header dessa requisição ele usou as duas opções para definir o tamanho do corpo (Content-Length e Transfer-Encoding). O Proxy segue a orientação da RFC e ignora o último campo e encaminha essa requisição para o back-end, todavia o back-end não respeita a RFC e ignora o primeiro campo, dessa forma a aplicação acredita que a requisição tem um tamanho menor do que ela tem de fato, fazendo com que o conteúdo “excedente” fique armazenado no back-end esperando a próxima conexão para que seja completado.

  
![](/assets/images/HTTP-Request-Smuggling/03.png){: .align-center}

Dessa maneira, o valor excedente da requisição do usuário se torna o início da requisição do próximo usuário como mostrado em laranja na figura acima. Vamos olhar mais de perto o que acontece no back-end para entendermos melhor:

  
![](/assets/images/HTTP-Request-Smuggling/04.png){: .align-center}

Nesse exemplo, a primeira requisição está em azul, ela passa pelo proxy pois ele segue a RFC e ignora o segundo campo e envia a requisição com o corpo contendo 6 bytes (contando os '\\r\\ n'), entretanto quando essa requisição chega ao back-end ele ignora o primeiro campo, fazendo com que a aplicação acredite que essa requisição termine no ‘0’.

Dessa maneira o ‘G’ fica armazenado no back-end aguardando a próxima requisição para que ela seja completada. Quando a requisição do segundo usuário (em verde) chega ao back-end, ela é concatenada com o ‘G’ que estava aguardando, fazendo com que a aplicação acredite que esse usuário fez uma requisição “GPOST”.

  

# Como detectar

A abordagem mais simples para detectar o HTTP Request Smuggling é enviando uma requisição com as duas opções de tamanho e, logo em seguida, fazer uma requisição normal para ver se a segunda solicitação vai retornar algum erro. O problema é que é extremamente dificil conseguir fazer essas duas requisições seguidas sem ter a interferência de outros usuários, o que nos atrapalha a identificar essa vulnerabilidade, uma vez que se outro usuário fizer a requisição depois 6 da nossa o erro vai aparecer para ele e não para a gente, o que vai fazer com que acreditemos que o site não é vulnerável.

Então para identificar essa vulnerabilidade vamos usar uma técnica que consiste em enviar uma sequência de mensagens e se o servidor for vulnerável ele vai travar e expirar a conexão, dessa forma poderemos reduzir os falsos positivos e o mais importante, não vamos afetar outros usuários.

Vamos imaginar que o Proxy use o Content-Length (CL) e o servidor use o TransferEncoding (TE). Para identificar o HTTP Request Smuggling podemos enviar a sequinte requisição:

  
![](/assets/images/HTTP-Request-Smuggling/05.png){: .align-center}

Como o Proxy usa o Content-Length para definir o tamanho do corpo da requisição, ele irá encaminhar para o back-end somente o que está em azul, fazendo com que o back-end fique aguardando o próximo tamanho do bloco até expirar, o que vai causar uma demora visível.

Se os dois servidores estiverem sincronizados, a requisição vai ser rejeitada pelo proxy ou processada de forma inofensiva pelos dois sistemas.

Outra possibilidade é de que a desincronização occora ao contrário TE.CL, nesse caso o proxy vai rejeitar a conexão e nunca irá encaminha-la para o back-end, por causa do tamanho do bloco inválido “Q”.

Podemos então detectar essa segunda forma de desincronização usando a seguinte solicitação:

  
![](/assets/images/HTTP-Request-Smuggling/06.png){: .align-center}

Como nesse caso o proxy utiliza o Transfer-Encoding para definir o tamanho do corpo da requisição, o bloco “0” faz com que o proxy encaminhe para o back-end somente o que está em azul, fazendo o back-end expirar aguardando a chegada do “X”.

Se a desincronização ocorrer ao contrário (CL.TE como no primeiro caso), essa abordagem irá envenenar o back-end com o “X”, podendo prejudicar outros usuários. Então é importante que os testes sigam essa ordem para que não ocorra nenhum problema.

  

# Exploração

Devido a complexidade desta vulnerabilidade fica muito dificil controlar os resultados e as consequências de um ataque em um ambiente real, então para mostrar uma exploração completa vou resolver uma máquina no estilo CTF onde será possível mostrar todos os passos da exploração e ao mesmo tempo não prejudicar ninguém.

Essa máquina está rodando uma aplicação bem simples e que possui uma página admin.

  
![](/assets/images/HTTP-Request-Smuggling/07.png){: .align-center}

Porém ao acessar a página admin ela retorna um erro 403 Forbidden, mostrando que não temos autorização para acessar essa página.

  
![](/assets/images/HTTP-Request-Smuggling/08.png){: .align-center}

O objetivo dessa máquina então é conseguir roubar as credenciais de outro usuário e conseguir acesso a página admin.

O primeiro passo é interceptar as requisições com a aplicação para identificar se ela está vulnerável ao HTTP Desync. Ao interceptar a requisição para o home do site já conseguimos algumas informações da aplicação, a primeira é que o servidor da aplicação é o gunicorn/20.0.0 e a segunda é que a aplicação está usando um load balancer que é o haproxy

  
![](/assets/images/HTTP-Request-Smuggling/09.png){: .align-center}

Vamos fazer um teste, vou mandar uma requisição POST para a aplicação para ver como ela se comporta, e como mostrado na imagem abaixo ele devolve a mesma resposta.

  
![](/assets/images/HTTP-Request-Smuggling/10.png){: .align-center}

Podemos então fazer um outro teste, vamos colocar na nossa requisição um conteúdo e vamos especificar no header da requisição o tamanho desse conteúdo com o Content-Length. Como podemos ver na imagem abaixo a resposta da aplicação continua a mesma.

  
![](/assets/images/HTTP-Request-Smuggling/11.png){: .align-center}

Agora vamos começar a identificar se a nossa aplicação está vulnerável, e para isso precisamos fazer mais alguns testes. No primeiro vou mandar um header com dois campos de content-length e ver como a aplicação se comporta.

  
![](/assets/images/HTTP-Request-Smuggling/12.png){: .align-center}

Dessa vez a resposta é diferente, recebemos um erro 400 Bad Request. Então vamos para o próximo teste, vamos substituir o segundo content-length por um Transfer-Encoding para ver qual vai ser a resposta da aplicação.

  
![](/assets/images/HTTP-Request-Smuggling/13.png){: .align-center}

Fazendo a requisição com os dois campos a aplicação retornou um 200 ok, interessante… vamos continuar os testes e enviar a mesma requisição mas dessa vez vamos trocar a ordem e enviar o Transfer-Encoding primeiro e ver qual vai ser a resposta.

  
![](/assets/images/HTTP-Request-Smuggling/14.png){: .align-center}

Como podemos ver na requisição, a aplicação mais uma vez retornou o status 200 ok, mesmo trocando a ordem do header, então temos duas possibilidades, ou o proxy está ignorando o campo Transfer-Encoding ou está ignorando o campo Content-Length. Nesse caso o que está acontecendo é que o proxy está ignorando o Content-Length e repassando para o servidor somente o conteúdo referente ao Transfer-Encoding.

Se de alguma maneira conseguirmos fazer o proxy ignorar o Transfer-Encoding e considerar somente o Content-Length para repassar toda nossa requisição para o servidor, mas se chegando lá o servidor utilizar só o Transfer-Encoding, por exemplo, por estar seguindo as orientações da RFC, então vamos conseguir uma vulnerabilidade de HTTP Desync.

Temos que ter em mente que a nossa requisição está sequindo o seguinte fluxo:

**Requisição → Proxy → Servidor**

Em alguns casos, o servidor proxy faz a correção do header da nossa requisição antes de enviar ela para o servidor, em outros ele simplesmente ignora a parte do header que tenha algum erro. Sabendo disso podemos usar essa correção para burlar o proxy e fazer com que ele envie nossa requisição para o servidor com os dois campos de tamanho.

Na pesquisa realizada pelo James Kettle ele descobriu que se substituirmos alguns caracteres da nossa requisição por caracteres especiais, alguns proxys irão corrigir a requisição e envia-la para o servidor, sem descartar o conteúdo.

No nosso caso, a aplicação que estamos explorando está usando o haproxy, e segundo a pesquisa do James se subistituirmos o espaço depois do campo Transfer-Encoding por um tab o haproxy vai fazer essa correção e enviar a requisição correta para o servidor, e vai ser isso que vou usar para resolver essa máquina.

Vamos voltar para nossa requisição, fazer essa alteração e ver como a aplicação vai se comportar.

  
![](/assets/images/HTTP-Request-Smuggling/15.png){: .align-center}

Como podemos ver, recebemos um 200 ok, o que significa que nossa requisição passou pelo proxy e chegou ao servidor sem problemas.

Vamos entender o que aconteceu nessa requisição. Como utilizamos um caractere especial no Transfer-Encoding o haproxy considerou que esse header estava errado e ignorou ele usando então o Content-Length, porém quando ele vai reenviar essa requisição para o servidor ele corrige esse caracter especial, enviando a requisição da forma correta para o servidor. Como o servidor está configurado para seguir o que está previsto na RFC ele ignora o último e prioriza o TransferEncoding para a requisição.

Agora já sabemos que o conteúdo da nossa requisição o ‘test’ está armazenado no buffer do servidor e está aguardando a próxima requisição que vai ser concatenada com ele, nesse momento já estamos conseguindo interferir nas conexões de outros usuários, dessa forma podemos agora começar a pensar em uma maneira de manipular as próximas requisições para resolver o desafio.

Algo que tinha passado desapercebido é que quando fazemos uma requisição para o diretório “ / ” do site ele nos redireciona para outro endereço, o problema é que ele usa o endereço que foi enviado pelo próprio usuário para fazer esse redirecionamento, essa é uma outra vulnerabilidade conhecida como Host Header Injection.

  
![](/assets/images/HTTP-Request-Smuggling/16.png){: .align-center}

Como a gente já sabe que conseguimos manipular as requisições de outros usuários, e agora sabemos que também conseguimos redirecionar os usuários e como o objetivo dessa máquina é acessar a área admin do site, que não temos permissão, podemos agora tentar redirecionar algum usuário legitimo para que possamos roubar as credênciais dele e acessar a área admin.

O corpo da nossa requisição será usado como o início da requisição do próximo usuário, vamos começar a preparar a nossa nova requisição para fazer o ataque. Como sabemos, a página que está fazendo o redirecionamento é a “ / ”, então temos que mandar o usuário para lá. Também sabemos que a requisição do usuário vai ser concatenada com a nossa, a melhor maneira de não causarmos um erro é concatenar essa requisição dentro de um parametro, nesse caso eu criei o parametro Test, também temos que informar o endereço para o qual queremos redirecionar o usuário, que nesse caso vai ser um servidor que está rodando na minha máquina. Sabendo de tudo isso a nossa requisição vai ficar mais ou menos assim:

  
![](/assets/images/HTTP-Request-Smuggling/17.png){: .align-center}

Agora que a nosso header está pronto, vou subir um netcat na minha máquina para saber se algum usuário vai ser redirecionado e então vou fazer a requisição.

  
![](/assets/images/HTTP-Request-Smuggling/18.png){: .align-center}

Como podemos ver na imagem acima, a nossa requisição foi realizada com sucesso e recebemos um 200 ok como resposta. Ao conferir o netcat que estava esperando por uma conexão constatamos que o nosso ataque deu certo e que recebemos uma conexão vinda de outro usuário, com o IP 10.9.0.10. O mais interessante é que nessa conexão recebemos também um Token, que muito provavelmente está sendo usado para validar os usuários que podem acessar a página admin.

  
![](/assets/images/HTTP-Request-Smuggling/19.png){: .align-center}

Como já sabemos um token válido de um usuário legítimo, podemos fazer uma nova requisição, mais dessa vez usando esse token para liberar o nosso acesso à página admin e dessa forma conseguir a nossa flag.

  
![](/assets/images/HTTP-Request-Smuggling/20.png){: .align-center}

# Como se defender

A simplicidade é amiga da segurança, e nesse caso não é diferente. Se o seu site não utiliza Load Balancers, CDNs e Reverse Proxies não há motivos para se preocupar. Como de costume, quanto mais camadas você colocar no seu site, mais chances você tem de ficar vulnerável.

Uma dúvida comum que surge é se utilizando o HTTPS estariamos seguros contra esse tipo de ataque, e a resposta é não. O HTTPS cria um túnel criptografado entre o cliente e o servidor, o que não é suficiente para nos proteger dessa vulnerabilidade, uma vez que ela ocorre quando o servidor interpreta nossa requisição.

Uma maneira de se proteger contra essa vulnerabilidade é configurando o Load Balancer / Reverse Proxy para utilizar somente o HTTP / 2 para se comunicar com o back-end ou desabilitar totalmente a reutilização da conexão com o back-end. Outra forma de se proteger é garantindo que todos os seus servidores utilizem o mesmo software de servidor com as mesmas configurações.

Em casos mais específicos essa vulnerabilidade pode ser resolvida configurando o Load Balancer / Reverse Proxy para normalizar as requisições ambíguas antes de envia-las ao servidor. Essa não é uma opção para o back-end, eles precisam rejeitar completamente essas solicitações e descartar a conexão. Como a rejeição de solicitações tem muito mais chance de afetar o tráfego legítimo é recomendado que se tente tratar essa vulnerabilidade nos Load Balancer / Reverse Proxy e não no back-end.

  

# Conclusão

Nesse artigo tentei demostrar como funciona o HTTP Desync e como essa vulnerabilidade pode ser perigosa, impactando desde a disponibilidade da aplicação até a obtenção de informações restritas.

Essa é uma vulnerabilidade muito pouco explorada e muito pouco estudada e que merece a nossa atenção, espero com esse artigo ter despertado o seu interesse nesse assunto e que a partir de agora você também desenvolva suas próprias pesquisas para que possamos desenvolver cada vez mais essa área.

Obrigado pela leitura!

  

# Referências

- James Kettle. HTTP Desync Attacks: Request Smuggling Reborn
- Ricardo Iramar dos Santos. The Powerful HTTP Request Smuggling
- Ori Hollander, Or Peles, Critical Vulnerability in HAProxy (CVE-2021-40346): Integer Overflow Enables HTTP Smuggling
- RFC2616. Hypertext Transfer Protocol
- Nginx. What is a Reverse Proxy vs. Load Balancer?
- Mdn Web Docs. An overview of HTTP
- Mdn Web Docs. Proxy servers and tunneling
- Mdn Web Docs. Transfer-Encoding
- Mdn Web Docs. Content-Length
