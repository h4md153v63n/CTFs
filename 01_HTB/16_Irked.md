# Irked

**Machine ip:** 10.10.10.117

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.117 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.117`



+ The result shows that only 1 TCP port is open:
```
Port 80: Apache httpd 2.4.18
```

+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ Navigate to `http://10.10.10.117/`



## Gaining Access
+ x


## Privilege Escalation
+ x


# References & Alternatives
+ https://vvmlist.github.io/#irked
+ 

