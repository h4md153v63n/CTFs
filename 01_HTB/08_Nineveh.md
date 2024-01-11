# Nineveh

**Machine ip:** 10.10.10.43

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.43 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.43`
+ `sudo nmap -Pn -sV -sC -sU -p$ports 10.10.10.43`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b0a28bb0-1ce5-4555-b8ea-43c6cb74d925)

+ `nineveh.htb` subdomain discovered.
+ Add: `echo "10.10.10.43 nineveh.htb" >> /etc/hosts`
+ Check for subdomains, and there is nothing.
```
wfuzz -c -u http://10.10.10.43/ -H "Host: FUZZ.nineveh.htb" -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt --hh 178
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/74415df0-f7e4-44e6-bf1c-6f634abeb157)

+ Navigate to `http://nineveh.htb/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c2011036-a4ab-432c-a4ad-ff243b9dcd85)

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -u http://nineveh.htb/ -x asp,aspx,cgi,htm,html,js,json,jsp,php,pl,py,sh -b 403,404
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6e68ff8c-ecb5-49c5-9e96-bf3c5facc224)

+ Navigate to `https://nineveh.htb/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/27f0d95d-b1e2-4e72-b56d-b8746e2e7505)

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -u https://nineveh.htb/ -x php,htm,html -b 403,404 -k
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/34694892-72fd-468e-80f8-28492c074369)







# References & Alternatives:
+ https://vvmlist.github.io/#nineveh
+ https://0xdf.gitlab.io/2020/04/22/htb-nineveh.html
+ 
