# FriendZone

**Machine ip:** 10.10.10.123

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.123 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.123`



+ The result shows that 2 TCP ports are open:
```
Port 22: OpenSSH 6.7p1
Port 80: Apache httpd 2.4.10

```

+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ Navigate to `http://10.10.10.123/`, and an irc application works.
+ View Page Source, and there's no useful information.



+ Directory fuzzing to enumerate directories:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.123/ -k -n -x html,php,txt -r -t 50
```


## Gaining Access
+ xxx



## Privilege Escalation
+ xxx


# References & Alternatives
+ https://vvmlist.github.io/#FriendZone
+ xxx

