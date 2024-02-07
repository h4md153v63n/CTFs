# FriendZone

**Machine ip:** 10.10.10.123

## Reconnaissance
First thing first, start with port scan to see which ports are open and which services are running on those ports.
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.123 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sort -n -k4 ports`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/42dc5726-6e75-4ad2-8f32-7f3359d98147)

+ `sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.123 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a3d1b012-e398-4443-a368-0713c3a947f3)

+ `sudo nmap -Pn -n -sV -sC -sU -O -p$ports 10.10.10.123 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ef8b3771-9af0-4ec8-a2e5-53372526c791)

+ The result shows that 7 tcp ports and 2 udp ports are open:
+ Open tcp ports:
```
Port tcp 21: vsftpd 3.0.3
Port tcp 22: OpenSSH 7.6p1 Ubuntu 4
Port tcp 53: ISC BIND 9.11.3-1ubuntu1.2
Port tcp 80: Apache httpd 2.4.29
Port tcp 139: Samba smbd 3.X - 4.X
Port tcp 443: Apache httpd 2.4.29
Port tcp 445: Samba smbd 4.7.6-Ubuntu
```
+ Open udp ports:
```
Port udp 53: ISC BIND 9.11.3-1ubuntu1.2
Port udp 137: Samba nmbd netbios-ns
```

Check port scan results:
+ Port tcp 21, ftp: Anonymous login is not allowed, then it requires credentials to access the FTP server. Moreover, the version is 3.0.3 which does not have any critical exploits.
+ Port tcp 22, ssh: There isn't any critical exploit associated with the version, so we need credentials for this service as well.
+ Port tcp/udp 53, dns: The first thing to do for this service is get the domain name with nslookup, and try a zone transfer to enumerate name servers, hostnames, etc. The ssl-cert from the port scan gives us the common name friendzone.red. Take note of it as it may be the target domain name.
+ Ports tcp 80/443, http/https are different page titles. It could be a virtual hosts routing configuration. If we discover other hosts, we need to enumerate them over both HTTP and HTTPS since they may give different results.
+ Ports tcp 139/445, and udp 137: Samba SMB ports are open. Check usual controls: try for anonymous login, list shares and check permissions on shares.


## Enumeration

### http-https
+ Add **friendzone.red** into the **/etc/hosts** file.
+ As usual, always start off with enumerating web server first. In this case both http and https are open.
+ Visit the web applications.
+ **Firstly**, navigate to `http://10.10.10.123/`, and take note of **info@friendzoneportal.red** which **friendzoneportal.red** may be a possible domain name.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c55ce1b0-e667-422a-ba94-3dda95e47fab)

+ View Page Source, and there's no useful information.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d19583f0-4d19-485b-aa3a-59d686b9cf91)

+ Directory fuzzing to enumerate directories:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.123/ -k -n -x html,php,txt -r -t 40
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f67ccdbb-3c35-45d0-91fe-49400d791e26)

+ Do more directory fuzzing on **wordpress** directory:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.123/wordpress -k -n -x html,php,txt -r -t 40
```

+ **wordpress** directory and **robots.txt** are empty.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1d71713b-aa5d-4238-9c06-54350b13d483)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/37b6a433-9b7a-45ab-8c5c-41c6676c7388)

+ There is nothing here.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d8a4d69e-0102-4fa9-873b-d0202f5644ab)


+ **Secondly**, navigate to `https://10.10.10.123/`, and it gives 'Not Found' error.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3cb404f7-d7af-479c-8b4f-9cdd6ac478fc)

+ Visit `https://friendzone.red/`, and see the https site is different from the http site.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b376d6c0-a098-429b-8fb7-9034436672ba)

+ Directory fuzzing to enumerate directories:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u https://friendzone.red/ -k -n -x html,php,txt -r -t 40
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/79a164c6-0165-4eef-a09e-cebdc97b7d35)

+ admin directory is empty,  and visit **https://friendzone.red/js**.
+ Check `https://friendzone.red/js/js/`, and there's nothing useful.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f9cbc884-adea-43d6-81e6-2335306495d1)


### smb
+ List available shares and permissions: `smbmap -H 10.10.10.123 `

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4c08c882-fdd7-4bae-9046-9639f94a64fb)

+ **Alternatively**, get a similar list without permissions from **smbclient** using **-N** for **null session** or **no auth** and **-L** to list:
```
smbclient -N -L //10.10.10.123
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/601c4600-35c8-456b-bd2f-a78696fa7ef7)

+ There are **READ** permission on the **general** share, and **READ/WRITE** permissions on the **Development** share.
+ And also results on the **Comment** column show that the files in the **Files** share are stored in **/etc/Files** on the system. Maybe **general** and **Development** follow the same pattern. No need to take risks using nmap nse:
```
nmap --script smb-enum-shares -p139,445 -T4 -Pn 10.10.10.123
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/174e2a79-5ada-46c5-810f-6ffbec20e8b8)

+ Take note of this piece of information as it may be required in the exploitation phase.

+ List recursively directories and files on all accessible shares: `smbmap -H 10.10.10.123 -r`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9c20d2ac-c6dd-40fb-9af2-9f2c6dcd031d)

+ There's nothing on the **Development** share, but the **general** directory has a file called **creds.txt**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a6fdb45c-29ea-4870-8e8e-5d351d74a793)

+ Login to null session general share anonymously without a password: `smbclient //10.10.10.123/general -N`
+ Download the creds.txt file from the target machine to the attack machine: `get creds.txt`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1308a185-f789-40f4-9468-ead9cc38def9)

+ View the content of the file: `cat creds.txt`
```
creds for the admin THING:

admin:WORKWORKHhallelujah@#
```

+ Try these credentials on ftp login and ssh login, but they don't work.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d2c9d528-d9b5-4d70-b646-4269b35e2de7)


### dns
TCP is only used in DNS when the response size is greater than 512 bytes. Typically, this is associated with Zone Transfers, where the server give all the information it has for a domain. There are a few things to try to enumerate DNS, but the fact that the host is listening on TCP 53 suggests the first thing to try is a Zone Transfer.
+ **Firstly**, **dig** with **friendzone.red** domain on tls certificate after port scan results:
```
dig axfr @10.10.10.123 friendzone.red
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4dfc3112-628c-428f-921b-4cfa30cbf12a)

+ **Secondly**, **dig** with **friendzoneportal.red** from 'info@friendzoneportal.red' mail address on the first webpage:
```
dig axfr @10.10.10.123 friendzoneportal.red
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8824b51e-478e-49d0-9340-afdf8cf92c50)

+ After these discovered subdomains, let's update hosts file for each of them.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/41fc16cd-582c-4049-9cf2-3860ebbf1d3a)

+ Check all of 8 subdomains:
```
friendzoneportal.red
administrator1.friendzone.red
hr.friendzone.red
uploads.friendzone.red
admin.friendzoneportal.red
files.friendzoneportal.red
imports.friendzoneportal.red
vpn.friendzoneportal.red
```

+ Visit `https://friendzoneportal.red`, and see Michael Jackson's image.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f3f025a0-5a0b-4b14-bc3e-0cd6eaf92f23)

+ Visit `https://administrator1.friendzone.red/`, andf there is a Login Form for FriendZone.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/604e18a2-6e68-4f64-8720-db50fd46d781)

+ `https://hr.friendzone.red/`: Not Found.
  
+ Visit `https://uploads.friendzone.red/`, and see upload page.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/938b0401-c97a-4e74-9523-73572dea06b7)

+ Visit `https://admin.friendzone.red`, and see the login panel.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a1523542-8f14-4f94-92c2-1e386ae2504a)

+ `https://files.friendzoneportal.red/`: Not Found.
+ `https://imports.friendzoneportal.red/`: Not Found.
+ `https://vpn.friendzoneportal.red/`: Not Found.

  





## Gaining Access
+ xxx



## Privilege Escalation
+ xxx


# References & Alternatives
+ https://vvmlist.github.io/#FriendZone
+ xxx

