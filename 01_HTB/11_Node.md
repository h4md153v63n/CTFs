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



# References & Alternatives:
+ https://0xdf.gitlab.io/2021/06/08/htb-node.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/node-writeup-w-o-metasploit
