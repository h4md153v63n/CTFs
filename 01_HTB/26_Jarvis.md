# Jarvis

**Links:** [1](https://www.hackthebox.com/machines/Jarvis)  [2](https://app.hackthebox.com/machines/Jarvis)

**Machine ip:** 10.10.10.143

## Reconnaissance
First thing first, start with port scan to see which ports are open and which services are running on those ports.
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.143 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.143 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/455ad88c-49a7-4563-9061-697165b520ef)

+ The result shows that 3 tcp ports are open:
```
Port tcp 22: OpenSSH 7.4p1
Port tcp 80: Apache httpd 2.4.25
Port tcp 64999: Apache httpd 2.4.25
```


## Enumeration
+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ **Firstly**, navigate to `http://10.10.10.143/`, and see the text.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c079fbca-b984-477c-bf02-10070494fc78)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e9bf486d-d84c-4518-872f-96733fa8c68f)

+ There are two discovered domain names. Add **supersecurehotel.htb** and **logger.htb** into the /etc/hosts file.
```
10.10.10.143 supersecurehotel.htb logger.htb
```

+ Both domain names redirect to the same website.
+ Next, view page source, and there's nothing useful.
+ As always, on each web app, start directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirb/big.txt -u http://10.10.10.143/ -k -n -x html,php,txt -r -t 1 --exclude-length 277
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d111fe65-2ea8-4b72-b000-84f025ce03aa)

+ Check the phpmyadmin directory `http://10.10.10.143/phpmyadmin`.
+ Try to login with default credentials [1](https://codeless.co/phpmyadmin-default-password/) [2](https://forum.terra-master.com/en/viewtopic.php?t=1239) but that didn't work.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2e7fe78c-03be-493e-ab28-c760807965ef)


+ Then visit all the links in the application, and then visit `Rooms & Suites -> Book now!`
+ The **room.php** page takes in a **cod** parameter, and outputs the related room information.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/96b77e0c-d506-43d0-94e7-9270c591359b)

+ **Secondly**, navigate to `http://10.10.10.143:64999/`, and see the text.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c6590eee-698b-4516-84e3-fbbbbff1759f)

+ Then, view page source, and there's nothing.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2b9cd031-9d75-46b3-b745-7205f63e1071)



## Gaining Access
+ xxx



### Shell Upgrade
+ We have partially interactive bash shell. Upgrade it to get a fully interactive shell, do more stable with a better shell.
```
python -c 'import pty; pty.spawn("/bin/bash")'
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
+ https://vvmlist.github.io/#jarvis
+ xxx


# For More
+ xxx

