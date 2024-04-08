# Optimum 

**OS:** Windows

**Level:** Easy

**Links:** [1](https://www.hackthebox.com/machines/Optimum)  [2](https://app.hackthebox.com/machines/Optimum)

**Machine ip:** 10.10.10.8

![Optimum](https://github.com/h4md153v63n/CTFs/assets/5091265/f0fe7004-231f-495f-b3b2-8af6d4e6d81a)



## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.8 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.8 --open
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6bd17850-1383-4bb9-9363-f840a535bbc5)


## Enumeration
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7b8a57ae-f7cc-4fee-a4bb-d9d8ae1f2ddb)

```
searchsploit httpfileserver

searchsploit -m 49125
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/93e024da-a49f-4962-99db-5f8d93e10d7e)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9449a98a-3a39-469c-a5ba-c2a2b311393d)

```
cp /usr/share/windows-binaries/nc.exe .

sudo python3 -m http.server

nc -nlvp 4444
```




## Exploitation & Gaining Access



## Privilege Escalation




# References & Alternatives
+ https://vvmlist.github.io/#Optimum
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
- x
