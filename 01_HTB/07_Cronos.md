# Cronos

**Machine ip:** 10.10.10.13

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.13 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.13`




# References & Alternatives:
+ https://vvmlist.github.io/#cronos
+ https://0xdf.gitlab.io/2020/04/14/htb-cronos.html
