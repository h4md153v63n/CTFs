# Nineveh

**Machine ip:** 10.10.10.43

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.43 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.43`
+ `sudo nmap -Pn -sV -sC -sU -p$ports 10.10.10.43`




# References & Alternatives:
+ https://vvmlist.github.io/#nineveh
+ https://0xdf.gitlab.io/2020/04/22/htb-nineveh.html
+ 
