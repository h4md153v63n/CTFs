# SwagShop

**Links:** [1](https://www.hackthebox.com/machines/swagshop)  [2](https://app.hackthebox.com/machines/swagshop)

**Machine ip:** 10.10.10.140

![SwagShop](https://github.com/h4md153v63n/CTFs/assets/5091265/73292ad2-0089-44f9-8d3c-a09b29f7a81d)

## Reconnaissance
First thing first, start with port scan to see which ports are open and which services are running on those ports.
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.140 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sort -n -k4 ports`



+ `sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.140 --open`



+ `sudo nmap -Pn -n -sV -sC -sU -O -p$ports 10.10.10.140 --open`



+ The result shows that 7 tcp ports and 2 udp ports are open:
+ Open tcp ports:
```
Port tcp 21: vsftpd 3.0.3
Port tcp 22: OpenSSH 7.6p1 Ubuntu 4
Port tcp 53: ISC BIND 9.11.3-1ubuntu1.2
Port tcp 80: Apache httpd 2.4.29
Port tcp 139: Samba smbd 3.X - 4.X
Port tcp 443: Apache httpd 2.4.29
Port tcp 445: Samba smbd 4.7.6-Ubuntu
```
+ Open udp ports:
```
Port udp 53: ISC BIND 9.11.3-1ubuntu1.2
Port udp 137: Samba nmbd netbios-ns
```

Check port scan results:
+ xxx


## Enumeration
+ xxx


## Gaining Access
+ xxx



## Privilege Escalation
+ xxx


# Technical Knowledge
+ xxx


# CVE Scripting
+ xxx


# References & Alternatives
+ https://vvmlist.github.io/#swagshop
+ xxx
+ 
