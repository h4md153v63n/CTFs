# SolidState
**Machine ip:** 10.10.10.51

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.51 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.51`



# References & Alternatives:
+ https://vvmlist.github.io/#solidstate
+ https://0xdf.gitlab.io/2020/04/30/htb-solidstate.html
