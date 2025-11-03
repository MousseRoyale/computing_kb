
---

**S1REN's Template Reference**

\[OS]: 
\[Web-Technology]: 
\[Hostname]:  
\[IP]:  192.168.2.150
\[USERS]:   
\[CREDENTIALS]:  

---
\[To-Try LIST]:  

**21 FTP**
Anon - key web files

**HTTP** 
80 - robots.txt
5801 - vnc-http TightVNC 1.2.9 (resolution: 1024x788; VNC TCP port 5901)

**22 SSH** - requires credentials

**111 RPC**
**2049 nfs**



**5901**/tcp open  vnc      VNC (protocol 3.7

---
\[NMAP RESULTS]:  

PORT     STATE SERVICE  VERSION
21/tcp   open  ftp      vsftpd (before 2.0.8) or WU-FTPD
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 0        0            2326 Nov 20  2004 apache_pb.gif
| -rw-r--r--    1 0        0            1385 Nov 20  2004 apache_pb.png
| -rw-r--r--    1 0        0            2410 Dec 14  2005 apache_pb22.gif
| -rw-r--r--    1 0        0            1502 Dec 14  2005 apache_pb22.png
| -rw-r--r--    1 0        0            2205 Dec 14  2005 apache_pb22_ani.gif
| -rw-r--r--    1 0        0             302 Mar 13  2006 favicon.ico
| -rw-r--r--    1 0        0              44 Nov 20  2004 index.html
|_-rw-r--r--    1 0        0              26 Dec 03  2008 robots.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.8.0.24
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 900
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 2.0.7 - secure, fast, stable
|_End of status
22/tcp   open  ssh      OpenSSH 5.1 (protocol 2.0)
| ssh-hostkey: 
|   1024 d5:18:d9:80:27:3b:4c:a0:cd:4c:e2:e0:4f:bc:e9:0f (DSA)
|_  1024 e1:65:e9:f4:c2:76:45:e2:40:45:ce:a0:69:fd:27:42 (RSA)
80/tcp   open  http     Apache httpd 2.2.10 ((Linux/SUSE))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.2.10 (Linux/SUSE)
|_http-favicon: Apache on Linux
| http-methods: 
|_  Potentially risky methods: TRACE
111/tcp  open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/udp   nfs
|   100005  1,2,3      38110/udp   mountd
|   100005  1,2,3      47763/tcp   mountd
|   100021  1,3,4      44369/tcp   nlockmgr
|   100021  1,3,4      54645/udp   nlockmgr
|   100024  1          35434/tcp   status
|_  100024  1          52427/udp   status
2049/tcp open  nfs      2-4 (RPC #100003)
5801/tcp open  vnc-http TightVNC 1.2.9 (resolution: 1024x788; VNC TCP port 5901)
|_http-title: Remote Desktop
5901/tcp open  vnc      VNC (protocol 3.7)
| vnc-info: 
|   Protocol version: 3.7
|   Security types: 
|     None (1)
|     Tight (16)
|   Tight auth subtypes: 
|     None
|_  WARNING: Server does not require authentication

--- 
\[Other Services]


---


\[Web Services Enumeration]:   

**Site exploration and page source:**

**80**
http://192.168.2.150/ -> nothing
 http-robots.txt: 1 disallowed entry 

**5801**
http://192.168.2.150:5801/ -> nothing



**Gobuster - Directorys**

`gobuster dir -u http://$IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 30

**80**
**5801**

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

