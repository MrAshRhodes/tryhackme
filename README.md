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

```nmap $IP -vvv -Pn -sV -oN nmap-output.txt```

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
