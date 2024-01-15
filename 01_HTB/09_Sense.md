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






# References & Alternatives:
+ https://vvmlist.github.io/#sense
+ https://0xdf.gitlab.io/2021/03/11/htb-sense.html
