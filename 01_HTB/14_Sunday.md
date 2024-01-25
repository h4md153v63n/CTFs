# Sunday

**Machine ip:** 10.10.10.84

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.84 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.84`



+ The result shows that 2 TCP ports are open:
```
Port 22: OpenSSH 7.2
Port 80: Apache httpd 2.4.29
```

+ Always start off with enumerating web server first.
+ Navigate to `http://10.10.10.84/`


## Gaining Access:



## Privilege Escalation:


# References & Alternatives:
+ https://vvmlist.github.io/#poison
+ 
