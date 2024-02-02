# Irked

**Machine ip:** 10.10.10.117

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.117 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.117`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a39552d9-85e5-491a-9959-bdbb8f0dd5c5)

+ The result shows that 7 TCP ports are open:
```
Port 22: OpenSSH 6.7p1
Port 80: Apache httpd 2.4.10
Port 111: rpcbind 2-4
Port 6697: irc
Port 8067: irc
Port 56060: irc
Port 65534: irc
```

+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ Navigate to `http://10.10.10.117/`, and an irc application works.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2f9bb664-f485-4f90-970d-bb4c2973f3dd)




## Gaining Access
+ x


## Privilege Escalation
+ x


# References & Alternatives
+ https://vvmlist.github.io/#irked
+ 

