# SolidState
**Machine ip:** 10.10.10.51

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.51 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.51`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/cffe61ef-f4fa-4ed9-a7b6-151d7fc033f5)

+ The result shows that 5 ports are open:
Port 22: OpenSSH 7.4p1
Port 25: JAMES smtpd 2.3.2
Port 80: httpd 2.4.25
Port 110: JAMES pop3d 2.3.2
Port 119: JAMES nntpd


# References & Alternatives:
+ https://vvmlist.github.io/#solidstate
+ https://0xdf.gitlab.io/2020/04/30/htb-solidstate.html
