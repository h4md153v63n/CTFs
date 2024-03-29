# Devel 

**Links:** [1](https://www.hackthebox.com/machines/Devel)  [2](https://app.hackthebox.com/machines/Devel)

**Machine ip:** 10.10.10.5

![devel](https://github.com/h4md153v63n/CTFs/assets/5091265/9ab7e4a6-088a-4d20-88e1-5bc25641cfa1)


## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.5 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.5 --open

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/fd580f7a-f9d8-4301-9fd7-82b27bb870e8)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/346d5392-a21f-4dde-b88b-5d98570477a1)


## Enumeration
```
ftp 10.10.10.5

Name (10.10.10.5:kali): anonymous
Password:

dir
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5149c861-836c-4c01-adf3-c5e5c3ad24d8)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/239327b2-ccb6-453a-9645-357665efbd90)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/be372bf9-cf9c-4971-b0a9-197bc61a24ae)





## Exploitation & Gaining Access 




## Privilege Escalation



# References & Alternatives
+ https://vvmlist.github.io/#devel
+ x


## CVE Scripting
+ x


## Tools
+ x


## Technical Knowledge
+ x


## Problems Solution
+ x


## For More
+ x
