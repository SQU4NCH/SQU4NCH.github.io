---
date: 2024-05-18 23:48:05
layout: post
title: Insecure Shop App - Resolvendo os desafios
subtitle: 
description: >-
  Resolução dos desafios do App Insecure Shop.
image: >-
  /assets/img/insecureshop/00.jpg
optimized_image: >-
  /assets/img/insecureshop/00.jpg
category: Write Up
tags:
  - Mobile
  - Android
  - Insecure Shop
author: squ4nch
paginate: false
---


InsecureShop é um aplicativo Android projetado para ser intencionalmente vulnerável. O aplicativo serve como uma plataforma para testar suas habilidades de pentesting no Android.

O aplicativo do projeto pode ser encontrado no repositório do GitHub nesse endereço: https://github.com/hax0rgb/InsecureShop

Nessa postagem eu vou mostrar como resolvi os desafios presentes no app.

# M7: Insufficient Binary Protection

Para iniciar o teste, foi realizado a engenharia reversa do aplicativo utilizando a ferramenta conhecida como “enjarify”.

![](/assets/img/insecureshop/m-01.png){: .align-center}

Também foi utilizado a ferramenta “apktool” para extrair todos os arquivos presentes no apk.

![](/assets/img/insecureshop/m-02.png){: .align-center}

# Credential HardCoded

Analisando o código fonte do app, foi possível encontrar as credenciais utilizadas para acesso ao aplicativo.

![](/assets/img/insecureshop/m-03.png){: .align-center}

Com as credenciais encontradas, foi possível acessar o app.

![](/assets/img/insecureshop/m-04.png){: .align-center}
![](/assets/img/insecureshop/m-05.png){: .align-center}

# MASTG-TEST-0003 - Testing Logs for Sensitive Data

Analisando o código fonte da aplicação, foi possível identificar que o app realiza o log das informações passadas para login e senha no aplicativo.

![](/assets/img/insecureshop/m-06.png){: .align-center}

Analisando os logs gerados pelo app, foi possível confirmar essa informação.

![](/assets/img/insecureshop/m-07.png){: .align-center}

```
shopuser:!ns3csh0p
```
# M9: Insecure Data Storage

Analisando o conteúdo da pasta “shared_prefs” do app, foi possível encontrar o arquivo “Prefs.xml” contendo o usuário e senha salvos em texto claro.

![](/assets/img/insecureshop/m-08.png){: .align-center}

# M8: Security Misconfiguration

## Bypass Trust Manager

Com acesso ao aplicativo, foi realizado a tentativa de acesso aos produtos listados pelo app, porém as informações não foram carregadas.

![](/assets/img/insecureshop/m-09.png){: .align-center}

Foi possível realizar o bypass da proteção utilizando o seguinte script frida público:

```javascript
Java.perform(function() {

    // TrustManager (Android < 7) //
    ////////////////////////////////
    var X509TrustManager = Java.use('javax.net.ssl.X509TrustManager');
    var SSLContext = Java.use('javax.net.ssl.SSLContext');
    var TrustManager = Java.registerClass({
        // Implement a custom TrustManager
        name: 'dev.asd.test.TrustManager',
        implements: [X509TrustManager],
        methods: {
            checkClientTrusted: function(chain, authType) {},
            checkServerTrusted: function(chain, authType) {},
            getAcceptedIssuers: function() {return []; }
        }
    });

    // Prepare the TrustManager array to pass to SSLContext.init()
    var TrustManagers = [TrustManager.$new()];
    // Get a handle on the init() on the SSLContext class
    var SSLContext_init = SSLContext.init.overload(
        '[Ljavax.net.ssl.KeyManager;', '[Ljavax.net.ssl.TrustManager;', 'java.security.SecureRandom');
    try {
        // Override the init method, specifying the custom TrustManager
        SSLContext_init.implementation = function(keyManager, trustManager, secureRandom) {
            console.log('[+] Bypassing Trustmanager (Android < 7) pinner');
            SSLContext_init.call(this, keyManager, TrustManagers, secureRandom);
        };
    } catch (err) {
        console.log('[-] TrustManager (Android < 7) pinner not found');
        //console.log(err);
    }

    // TrustManagerImpl (Android > 7) //
    ////////////////////////////////////
    try {
        // Bypass TrustManagerImpl (Android > 7) {1}
        var array_list = Java.use("java.util.ArrayList");
        var TrustManagerImpl_Activity_1 = Java.use('com.android.org.conscrypt.TrustManagerImpl');
        TrustManagerImpl_Activity_1.checkTrustedRecursive.implementation = function(certs, ocspData, tlsSctData, host, clientAuth, untrustedChain, trustAnchorChain, used) {
            console.log('[+] Bypassing TrustManagerImpl (Android > 7) checkTrustedRecursive check for: '+ host);
            return array_list.$new();
        };
    } catch (err) {
        console.log('[-] TrustManagerImpl (Android > 7) checkTrustedRecursive check not found');
        //console.log(err);
        errDict[err] = ['com.android.org.conscrypt.TrustManagerImpl', 'checkTrustedRecursive'];
    }  
    try {
        // Bypass TrustManagerImpl (Android > 7) {2} (probably no more necessary)
        var TrustManagerImpl_Activity_2 = Java.use('com.android.org.conscrypt.TrustManagerImpl');
        TrustManagerImpl_Activity_2.verifyChain.implementation = function(untrustedChain, trustAnchorChain, host, clientAuth, ocspData, tlsSctData) {
            console.log('[+] Bypassing TrustManagerImpl (Android > 7) verifyChain check for: ' + host);
            return untrustedChain;
        };  
    } catch (err) {
        console.log('[-] TrustManagerImpl (Android > 7) verifyChain check not found');
        //console.log(err);
        errDict[err] = ['com.android.org.conscrypt.TrustManagerImpl', 'verifyChain'];  
    }
});
```

Dessa forma, foi possível realizar o bypass nessa proteção.

![](/assets/img/insecureshop/m-10.png){: .align-center}

Também foi possível interceptar as requisições realizadas pelo aplicativo.

![](/assets/img/insecureshop/m-11.png){: .align-center}

E assim, acessar o conteúdo dos produtos presentes no app.

![](/assets/img/insecureshop/m-12.png){: .align-center}

Analisando as requisições geradas pelo aplicativo, foi possível notar que a comunicação realizada pelo app e o servidor não possuem criptografia.

![](/assets/img/insecureshop/m-13.png){: .align-center}

# Insufficient URL Validation

Analisando o conteúdo do arquivo “AndroidManifest.xml” foi possível identificar a classe responsável pelo _webview_.

![](/assets/img/insecureshop/m-14.png){: .align-center}

Realizando a análise do código fonte dessa classe, é possível identificar que o app realiza uma verificação, porém acaba carregando a url passada como argumento.

![](/assets/img/insecureshop/m-15.png){: .align-center}

Dessa forma, é possível carregar conteúdo de outros sites dentro do app.

![](/assets/img/insecureshop/m-16.png){: .align-center}
![](/assets/img/insecureshop/m-17.png){: .align-center}

# AWS Cognito Misconfigurations

Analisando o arquivo “strings.xml” presente nos arquivos do apk, é possível encontrar o ID do _Identity Pool_ da AWS.

![](/assets/img/insecureshop/m-18.png){: .align-center}

Com a informação encontrada, é possível realizar uma consulta e descobrir o _Identity ID_ da acesso a AWS.

![](/assets/img/insecureshop/m-19.png){: .align-center}

Com o identity id, é possível descobrir mais informações

![](/assets/img/insecureshop/m-20.png){: .align-center}
