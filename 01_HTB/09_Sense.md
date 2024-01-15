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
feroxbuster -u https://10.10.10.60 -x html,php -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --depth 2 -C 403,404,500 -W 0 -r --extract-links -k -x asp,aspx,cgi,htm,html,js,json,jsp,php,pl,py,sh
```


# References & Alternatives:
+ https://vvmlist.github.io/#sense
+ https://0xdf.gitlab.io/2021/03/11/htb-sense.html
