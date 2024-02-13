# Networked

**Links:** [1](https://www.hackthebox.com/machines/Networked)  [2](https://app.hackthebox.com/machines/Networked)

**Machine ip:** 10.10.10.146

![networked](https://github.com/h4md153v63n/CTFs/assets/5091265/92cd2294-7d07-497d-8f0d-7ed00d4d5c9f)

## Reconnaissance
First thing first, start with port scan to see which ports are open and which services are running on those ports.
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.146 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.146 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/75a583b5-e3f7-4d7d-bc33-12c1747da5ec)

+ The result shows that 2 tcp ports are open:
```
Port tcp 22: OpenSSH 7.4
Port tcp 80: Apache httpd 2.4.6
```


## Enumeration
+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ Navigate to `http://10.10.10.146/`, and see the text.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dfa167a8-c0a2-414c-8dd2-278a62540c7c)

+ Viev page source, and take note of **upload** with **galery** paths.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a06ac56d-8266-4ee5-99e1-96d5284435cc)

+ As always, on each web app, start directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.146/ -k -n -x html,php,txt -r -t 30
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/360da068-3dd3-412f-80f2-5b85dd3d5ae4)

+ Visit **upload.php** page.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/02cf635c-1554-499f-a6ce-f996bf402e30)

+ Next, visit the **backup** directory. It contains a compressed file.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6e294d32-8a7e-4eda-9d1f-28aca1301525)





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
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/networked-writeup-w-o-metasploit
+ xxx


# For More
+ xxx
