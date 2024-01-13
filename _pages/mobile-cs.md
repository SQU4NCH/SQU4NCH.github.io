---
title: "Mobile Cheat Sheet"
permalink: /mobile-cs/
layout: archive
author_profile: true
---

# Android
## Bypass Lock Screen

**Dispositivo precisa estar como root**

1 - Acessar o dispositivo

```
adb shell
```

2 - Ir até a pasta com o arquivo

```
cd /data/system
```

3 - Remover o arquivo "gesture.key"

```
rm gesture.key
```
**ou outros arquivos com extensão .key**

## ADB - Android Debug Bridge
Lista devices
```
adb devices
(-d devices | -e emuladores | -s serial)
```

Concede uma shell
```
adb shell
```

Executa um comando direto
```
adb shell [comando]
```

Copia dados para o android
```
adb push [arquivo] [path do android]
```

Pega dados do android
```
adb pull [path do android]
```

Instala um app manualmente
```
adb install exemple.apk
```

Desinstala um app
```
adb uninstall [package]
```

Encerra o serviço do ado
```
adb kill-server
```

Inicia o serviço do adb
```
adb start-server
```
## PM - Package Manager

Lista usuários
```
pm list users
```

Lista todos os pacotes
```
pm list packeges
```

Lista todos os pacotes de sistema
```
pm list packeges -s
```

Lista todos os pacotes de terceiros
```
pm list packeges -3
```

Mostra o caminho do apk
```
pm path packege_name
```

Desinstala o pacote
```
pm uninstall packege_name
```

## AM - Activity Manager

A Activity realiza uma ação no Android

Site oficial:
<a href="https://developer.android.com/guide/appendix/app-intents" target="_blank">https://developer.android.com/guide/appendix/app-intents</a><br>

Ex:

Ver um site
```
am start -a android.intent.action.VIEW https://[site]
```

Ver contatos
```
am start -a android.intent.action.VIEW content://contacts/people
```

Ligar para um número
```
am start -a android.intent.action.CALL -d tel:[numero]
```

## Componentes do Android

### Activities

Abrindo player de música
```
am start -n com.android.music/com.android.music.MusicBrowserActivity
```

### Services

Subindo serviço do player de música (Back ground)
```
am startservice -n com.android.music/com.android.music.MediaPlaybackService
```

### Broadcast Receivers

Mostrando mensagem do Android ligado com sucesso
```
am broadcast -a android.intent.action.BOOT_COMPLETED
```

### Content Providers

Visualizando mensagens enviadas
```
content query --uri content://sms/sent
```

## Processo de compilação de um apk

### JVM - Java Virtual Machine

A JVM executa o bytecode gerado após a compilação
```
.java => javac => .class (bytecode)
```

### DVM - Dalvik Virtual Machine

O bytecode gerado pela JVM é usado como entrada para gerar um formato mais leve em um único de arquivo conhecido como Dalvik Executable (.dex) que é executado pela DVM
```
.java => javac => .class (bytecode) => dx => .dex (bytecode)
```

### ART - Android Runtime

Ao invés da interpretação pela VM ele permite a execução nativa do código pelo processador
```
.java => javac => .class (bytecode) => dx => .dex (bytecode) => dex2oat => Binário Nativo
```

## Baixando APKs

### Via Internet

Entrar no site da google play store e procurar pelo app

<a href="https://play.google.com/" target="_blank">https://play.google.com/</a><br>

Copiar o link do app e colar no site abaixo para fazer o download

<a href="https://apps.evozi.com/apk-downloader/" target="_blank">https://apps.evozi.com/apk-downloader/</a><br>
<a href="https://apkpure.com/" target="_blank">https://apkpure.com/</a><br>
<a href="https://apkcombo.com/" target="_blank">https://apkcombo.com/</a><br>

### Via Dispositivo Físico

Baixar o app normalmente na loja da play store

Após isso conectar o dispositivo no computador e copiar o apk via adb para a máquina

### Via Emulador

Instalar uma versão do Android no Android Studio que possua a play store e repetir os mesmos passos do dispositivo físico

# Engenharia Reversa
## APKTOOL

Um app (.apk) nada mais é que um arquivo compactado, é possível descompacta ele para ver a sua estrutura, porém, não será possível ver o conteúdo dos arquivos pois eles estão compilados

Para isso é possível utilizar a ferramenta "apktool" que consegue decompilar o arquivo .apk

```
java -jar apktool.jar d [aplicativo.apk] -o [destino]
```

Com o "apktool" também é possível fazer o caminho inverso, construindo um apk a partir do código modificado

## SMALI e BAKSMALI

A linguagem SMALI seria ao equivalente ASSEMBLY para Android

O SMALI / BAKSMALI seria o ASSEMBLER / DISASSEMBLER para o formato .dex

***"smali/baksmali is an assembler/disassembler for the dex format used by dalvik, Android's Java VM implementation. The syntax is loosely based on Jasmin's/dedexer's syntax, and supports the full functionality of the dex format (annotations, debug info, line info, etc.)***

***The names "smali" and "baksmali" are the Icelandic equivalents of "assembler" and "disassembler" respectively. Why Icelandic you ask? Because dalvik was named for an Icelandic fishing village."***

Referência:
<a href="https://github.com/JesusFreke/smali/wiki" target="_blank">https://github.com/JesusFreke/smali/wiki</a><br>

Transformando um apk em formato smali:

```
java -jar baksmali.jar d [app.apk]
```

## Dex2JAR e JD-GUI

Essa técnica consiste em utilizar duas ferramentas para fazer a engenharia reversa do app, trazendo o código o mais próximo possível do seu código real em Java

Transformando o apk para java
```
dex2jar [app.apk] -o [file.jar]
```

Lendo o código Java
```
java -jar jd-gui.jar [file.jar]
```

## JADX

Outra forma de transformar o apk em código java é utilizando o "JADX"

Modo linha de comando
```
jadx [app.apk] -d [pasta]
```

Modo com interface gráfica
```
jadx-gui [app.apk]
```

