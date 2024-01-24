# Poison

**Machine ip:** 10.10.10.84

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.84 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.84`



+ The result shows that 2 TCP ports are open:
```
Port 22: OpenSSH 5.9p1
Port 80: Apache httpd 2.2.22 - http
```

+ Always start off with enumerating web server first.
+ Navigate to `http://10.10.10.84/`



+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirb/common.txt -u 10.10.10.84 -k -n -r -t 40
```



# References & Alternatives:
+ https://vvmlist.github.io/#poison
+ 
