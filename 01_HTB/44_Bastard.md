# Bastard 

**OS:** Windows

**Level:** Medium

**Links:** [1](https://www.hackthebox.com/machines/Bastard)  [2](https://app.hackthebox.com/machines/Bastard)

**Machine ip:** 10.10.10.9

![Bastard](https://github.com/h4md153v63n/CTFs/assets/5091265/84d54684-632c-4ac5-ac8e-146a4b2d7267)


## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.9 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.9 --open
```




## Enumeration



## Exploitation & Gaining Access



## Privilege Escalation



# References & Alternatives
+ https://vvmlist.github.io/#Bastard
+ 


## CVE Scripting
+ 


## Tools
+ 


## Technical Knowledge
+ 


## Problems Solution
+ 


## For More
+  
