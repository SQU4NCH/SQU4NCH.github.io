---
title: "Interceptando tráfego no Android"
classes: wide
header:
  teaser: /assets/images/burp-Android/00.jpg
  overlay_image: /assets/images/burp-Android/00.jpg
  overlay_filter: 0.5
ribbon: DarkSlateGray
excerpt: "Breve tutorial sobre a configuração do Android para interceptação de tráfego web através do Burp"
description: "Breve tutorial sobre a configuração do Android para interceptação do tráfego web através do Burp"
categories:
  - Mobile
tags:
  - Mobile
  - Android
  - Burp
toc: true
toc_sticky: true
toc_label: "Sumário"
toc_icon: "terminal"
---

Quando falamos de testes em aplicativos mobile, conseguir interceptar o tráfego do app com a API / Backend é fundamental para o sucesso da exploração. Nesse post vou mostrar uma das maneiras possíveis de se fazer isso.

# Configurando o proxy
## No burp

Antes de tudo, é necessário se certificar que o burp está utilizando o endereço "127.0.0.1:8080". Então abra o burp e vá em Proxy -> Proxy settings -> Proxy para confirmar essa informação.

![](/assets/images/burp-Android/M-01.png){: .align-center}

## No dispositivo

O proxy será configurado através da rede Wi-Fi do dispositivo e durante o teste algumas coisas podem acabar barrando essa comunicação, como por exemplo uma proteção presente no app ou qualquer outro problema de conexão. Quando isso acontece, o sistema operacional Android entende que aquela rede Wi-Fi não possui conexão com a internet e começa a rotear os seus pacotes através dos dados móveis, o que faz com que não consigamos mais interceptar seu tráfego, para evitar que isso aconteça é necessário desabilitar os dados móveis no aparelho.

Para isso, basta arrastar a barra superior para baixo e clicar no ícone do LTE.

![](/assets/images/burp-Android/M-02.png){: .align-center}

Após isso é só confirmar o desligamento dos dados móveis.

![](/assets/images/burp-Android/M-03.png){: .align-center}

Com os dados móveis desligados, vamos realizar um port forwarding para criar um túnel que redireciona todo o tráfego do dispositivo para a máquina local na porta 8080.

Para isso, basta executar o seguinte comando do adb:

```
adb reverse tcp:8080 tcp:8080
```

Após a criação do túnel, chegou a hora de configurar o proxy no aparelho.

Novamente arraste a barra superior para baixo, então clique e segure o símbolo do Wi-Fi.

![](/assets/images/burp-Android/M-04.png){: .align-center}

Então, aparecerá as redes disponíveis para o dispositivo, novamente clique e segure na rede "AndroidWifi".

![](/assets/images/burp-Android/M-05.png){: .align-center}

Nas opções que irão aparecer vá em "Modify network".

![](/assets/images/burp-Android/M-06.png){: .align-center}

Então altere o proxy para manual e coloque as informações do burp:

![](/assets/images/burp-Android/M-07.png){: .align-center}

Com isso já é possível visualizar o tráfego do dispositivo através do burp.

![](/assets/images/burp-Android/M-08.png){: .align-center}

# Instalando o certificado no Android

Como pode ser visto no exemplo acima, apesar de o tráfego estar sendo interceptado, não foi possível acessar o site do Google. Analisando os logs do burp é possível identificar o problema.

![](/assets/images/burp-Android/M-09.png){: .align-center}

O erro foi causado pelo certificado, o que faz com que o cliente não consiga acessar sites que utilizem o TLS. Para resolver esse problema é necessário instalar o certificado do burp no dispositivo Android.

Para realizar o download do certificado, acesse o endereço "http://burp" do navegador da sua máquina.

![](/assets/images/burp-Android/M-10.png){: .align-center}

O certificado baixado possui o nome "cacert.der", para realizar a instalação no dispositivo, é necessário alterar a sua extensão para ".cer".

```
mv cacert.der cacert.cer
```

Após isso, envie o arquivo do certificado para o dispositivo Android.

```
adb push cacert.cer /sdcard/Download/cacert.cer
```

Agora, no dispositivo vá em "Settings".

![](/assets/images/burp-Android/M-11.png){: .align-center}

Depois em "Security & location".

![](/assets/images/burp-Android/M-12.png){: .align-center}

Depois em "Encryption & credentials".

![](/assets/images/burp-Android/M-13.png){: .align-center}

E por fim em "Install from SD card".

![](/assets/images/burp-Android/M-14.png){: .align-center}

Ao abrir essa opção, vá no menu presente no canto superior esquerdo e em seguida abra a pasta de Downloads.

![](/assets/images/burp-Android/M-15.png){: .align-center}
![](/assets/images/burp-Android/M-16.png){: .align-center}

Pronto, ai está o certificado.

![](/assets/images/burp-Android/M-17.png){: .align-center}

Agora basta clicar no arquivo, escolher o nome do certificado e realizar a instalação.

![](/assets/images/burp-Android/M-18.png){: .align-center}

** *Nesse momento, pode ser que o aparelho peça para você configurar uma senha para o dispositivo, esse é um mecanismo de segurança padrão do Android e basta seguir o passo a passo.*

Pronto, agora já é possível acessar os sites utilizando o https.

![](/assets/images/burp-Android/M-19.png){: .align-center}