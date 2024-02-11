# Networked

**Links:** [1](https://www.hackthebox.com/machines/Networked)  [2](https://app.hackthebox.com/machines/Networked)

**Machine ip:** 10.10.10.146

![networked](https://github.com/h4md153v63n/CTFs/assets/5091265/92cd2294-7d07-497d-8f0d-7ed00d4d5c9f)

## Reconnaissance
First thing first, start with port scan to see which ports are open and which services are running on those ports.
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.146 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.146 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/936de3e3-86b1-4006-a7ef-6a3f0fe2aa32)

+ The result shows that 2 tcp ports are open:
```
Port tcp 22: OpenSSH 7.6p1
Port tcp 80: Apache httpd 2.4.29
```


## Enumeration
+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ Navigate to `http://10.10.10.146/`, and no works.



+ Add **.htb** into **hosts** file, and again revisit.



+ The application is xxx that is an open-source e-commerce platform written in PHP.
+ As always, on each web app, start directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://xxx.htb/ -k -n -x html,php,txt -r -t 50
```


## Gaining Access



### Shell Upgrade
+ We have partially interactive bash shell. Upgrade it to get a fully interactive shell, do more stable with a better shell.
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
script /dev/null -c bash
CTRL^Z: Ctrl + Z
stty raw -echo; fg
reset
terminal type? 'screen' or 'xterm'
export TERM=xterm  
export SHELL=bash
stty rows 55 columns 285
```


## Privilege Escalation
+ Escalate privileges to get the root flag.



# Technical Knowledge
+ xxx


# CVE Scripting
+ xxx


# References & Alternatives
+ https://vvmlist.github.io/#Networked
+ https://0xdf.gitlab.io/2019/11/16/htb-networked.html
+ xxx


# For More
+ xxx
