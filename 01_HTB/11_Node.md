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

+ Check `http://10.10.10.58:3000/assets/js/app/controllers/home.js`

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






# References & Alternatives:
+ https://0xdf.gitlab.io/2021/06/08/htb-node.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/node-writeup-w-o-metasploit
