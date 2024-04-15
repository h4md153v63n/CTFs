# Granny

**OS:** Windows

**Level:** Easy

**Links:** [1](https://www.hackthebox.com/machines/Granny)  [2](https://app.hackthebox.com/machines/Granny)

**Machine ip:** 10.10.10.15

![Granny](https://github.com/h4md153v63n/CTFs/assets/5091265/69e37688-dfcb-49cc-810a-ec81fec1581b)


## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.15 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.15 --open
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ec0f4480-c9e2-4ca0-a2e8-0d68e25572cc)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/212f7ec1-e525-4478-9288-149a9f5e5ec4)


## Enumeration
Visit **http://10.10.10.15**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6c195f8b-51e0-4e25-bb23-ac0b0deee900)





## Exploitation & Gaining Access

### Method 1: 



## Privilege Escalation

### Method 1: 



# References & Alternatives
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
