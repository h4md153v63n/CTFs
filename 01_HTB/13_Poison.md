# Poison

**Machine ip:** 10.10.10.84

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.84 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.84`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dc6230c3-bc5c-468e-821e-514f8e0828fd)

+ The result shows that 2 TCP ports are open:
```
Port 22: OpenSSH 7.2
Port 80: Apache httpd 2.4.29
```

+ Always start off with enumerating web server first.
+ Navigate to `http://10.10.10.84/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1c32c4dc-e26f-4728-ba3e-1518b918c928)

+ When entering **listfiles.php** into **Scriptname** textbox, **pwdbackup.txt** is interesting.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bb8237de-e6cc-41e0-8bef-d003d7e1ae08)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a643911b-431e-4fb0-aea8-fcce9e4e28aa)

+ Enter **pwdbackup.txt** into the textbox, and you'll see base64 encoding.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/755d1b04-96a4-44ce-936f-87f6ff1d3753)

+ This means that the application is vulnerable to local file inclusion (LFI).





# References & Alternatives:
+ https://vvmlist.github.io/#poison
+ 
