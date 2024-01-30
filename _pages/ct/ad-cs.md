---
title: "AD Cheat Sheet"
permalink: /ct/ad-cs/
layout: archive
author_profile: true
---

## Sem Credenciais
### Scan Network

Enumerando smb hosts

```
cmd smb <ip_range>
```

Ping scan

```
nmap -sP -p <ip>
```

Quick scan

```
nmap -PN -sV --top-ports 50 --open <ip>
```

Search smb vuln

```
nmap -PN --script smb-vuln* -p 139,445 <ip>
```

Classic Scan

```
nmap -PN -sC -sV <ip>
```

Full Scan

```
nmap -PN -sC -sV -p- <ip>
```

UDP Scan

```
nmap -sU -sC -sV <ip>
```

### Find AD IP

Show domain name & dns

```
nmcli dev show eth0
```

```
nslookup -type=SRV _ldap._tcp.dc_msdcs.//DOMAIN/
```

### Zone Transfer

```
dig axfr <domain_name> @<name_server>
```

### List guest access on smb share

```
enum4linux -a -u "" -p "" <dc-ip> && enum4linux -a -u "guest" -p "" <dc-ip>
```

```
smbmap -u "" -p "" -P 445 -H <dc-ip> && smbmap -u "guest" -p "" -P 445 -H <dc-ip>
```

```
smbclient -U '%' -L //<dc-ip> && smbclient -U 'guest%' -L //<dc-ip>
```

Enumerando null session

```
cme smb <ip> -u '' -p ''
```

Enumerando acesso anonymous

```
cme smb <ip> -u 'a' -p ''
```

### Enumerate ldap

```
nmap -n -sV --script "ldap* and not brute" -p 389 <dc-ip>
```

```
ldapsearch -x -h <ip> -s base
```

### Find user list

```
enum4linux -U <dc-ip> | grep 'user:'
```

```
crackmapexec smb <ip> -u <user> -p '<password>' --users
```
