# Blue 

**Links:** [1](https://www.hackthebox.com/machines/Blue)  [2](https://app.hackthebox.com/machines/Blue)

**Machine ip:** 10.10.10.40

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a8b31b09-f318-49b3-a8b6-0faa07b52c0d)



## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.40 -e tun0 > ports

sort -n -k4 ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.40 --open

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/541cf98d-1908-45af-a6ea-4bfc302fdf55)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1acc49b7-1b38-4c2f-8f43-2c7d42f91b00)


## Enumeration
```
nmap --script vuln 10.10.10.40
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/be27653c-bed5-41e6-bf76-9b531c32a044)


## Exploitation & Gaining Access



## Privilege Escalation




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
