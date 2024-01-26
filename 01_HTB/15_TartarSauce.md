# TartarSauce

**Machine ip:** 10.10.10.76

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.76 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.76`



+ The result shows that 5 TCP ports are open:
```
Port 79: finger?
Port 111: rpcbind 2-4
Port 515: printer
Port 6787: http
Port 22022: OpenSSH 8.4
```

+ Start off with enumerating port 79, and it is **finger** service.



## Gaining Access
+ xxx



## Privilege Escalation
+ xxx



# References & Alternatives
+ https://vvmlist.github.io/#TartarSauce
+ https://0xdf.gitlab.io/2018/10/20/htb-tartarsauce.html
+ xxx

  
