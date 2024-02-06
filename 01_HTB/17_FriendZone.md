# FriendZone

**Machine ip:** 10.10.10.123

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.123 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sort -n -k4 ports`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/42dc5726-6e75-4ad2-8f32-7f3359d98147)

+ `sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.123 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a3d1b012-e398-4443-a368-0713c3a947f3)

+ `sudo nmap -Pn -n -sV -sC -sU -O -p$ports 10.10.10.123 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ef8b3771-9af0-4ec8-a2e5-53372526c791)

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

