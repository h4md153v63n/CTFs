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
gobuster dns -d nineveh.htb -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/63ad2d6e-c2e7-461f-9ee0-77c476454634)

+ Navigate to `http://nineveh.htb/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c2011036-a4ab-432c-a4ad-ff243b9dcd85)

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirb/common.txt -u http://nineveh.htb/ -b 403,404
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2f1439c6-9f22-4b06-8c53-eca3877bb296)

+ Navigate to `https://nineveh.htb/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/27f0d95d-b1e2-4e72-b56d-b8746e2e7505)

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirb/common.txt -u https://nineveh.htb/ -b 403,404 -k
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/14cf9201-76d1-41ef-b34c-dc090ab04004)




# References & Alternatives:
+ https://vvmlist.github.io/#nineveh
+ https://0xdf.gitlab.io/2020/04/22/htb-nineveh.html
+ 
