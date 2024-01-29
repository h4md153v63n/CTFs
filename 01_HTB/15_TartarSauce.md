# TartarSauce

**Machine ip:** 10.10.10.88

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.88 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.88`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e468c3a4-979b-4729-8cd7-db225339bf95)

+ The result shows that only 1 TCP port is open:
```
Port 80: Apache httpd 2.4.18
```

+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ Navigate to `http://10.10.10.88/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1a3fed0d-ed3c-4538-ab8f-86b7cdd88fa8)




## Gaining Access
+ xxx



## Privilege Escalation
+ xxx



# References & Alternatives
+ https://vvmlist.github.io/#TartarSauce
+ https://0xdf.gitlab.io/2018/10/20/htb-tartarsauce.html
+ xxx

  
