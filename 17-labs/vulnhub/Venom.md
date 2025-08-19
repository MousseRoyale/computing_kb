https://www.vulnhub.com/entry/venom-1,701/


---

**S1REN's Template Reference**

\[OS]: Unix
\[Web-Technology]: 
\[Hostname]:  
\[IP]:  192.168.100.4
\[USERS]:   
\[CREDENTIALS]:  hostinger

---
\[To-Try LIST]:  
21:  ftp        vsftpd 3.0.3
80: Apache httpd 2.4.29 ((Ubuntu)) 
443: Apache httpd 2.4.29




---
\[NMAP RESULTS]:  

PORT     STATE  SERVICE    VERSION
21/tcp   open   ftp        vsftpd 3.0.3
22/tcp   closed ssh
80/tcp   open   http       Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
443/tcp  open   http       Apache httpd 2.4.29
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
7070/tcp closed realserver
8084/tcp closed websnp

\[Web Services Enumeration]:   

**80 and 443:**  http://192.168.100.4:80,http://192.168.100.4:443
Apache httpd 2.4.29: https://www.exploit-db.com/exploits/744
https://launchpad.net/bugs/1288690 -> CVE 2016-4979
Page Source: 5f2a66f947fa5690c26506f66bde5c23 -> Crackstation -> hostinger
Gobuster - /server-status





========================================================================= \[OTHER / PRIVILEGE-ESCALATION]:   ========================================================================= \Take Away Concepts: *  *  *

    
    HOTKEYS! Background: Black Foreground (important elements): Red Background-Color: Select Text > Shift + Alt + B + ENTER Foreground Color: Select Text > Shift + Alt + F + ENTER Move quickly with: Function key + left and right arrows Cntrl + left and right arrows Cntrl + Shift (select text) (edited)
