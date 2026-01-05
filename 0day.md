I started by running an Nmap scan to see what services were exposed on the target machine. This helps understand the attack surface before diving deeper.

First, I ran a standard scan with default scripts and version detection:

```bash
mrbunny $ sudo nmap -sS -p- 10.80.160.139 -oN port-scan.txt -T5
[sudo] password for greatkey: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-01-04 21:49 EST
Stats: 0:00:01 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 0.46% done
Stats: 0:00:40 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 56.47% done; ETC: 21:50 (0:00:32 remaining)
Stats: 0:00:42 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 60.39% done; ETC: 21:50 (0:00:28 remaining)
Nmap scan report for 10.80.160.139
Host is up (0.15s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 55.52 second
```



The scan revealed only two open ports:

22/tcp – SSH

80/tcp – HTTP

Since SSH typically requires valid credentials, I decided to focus on the web service running on port 80, as web applications often provide an initial entry point.

<img width="1920" height="1080" alt="Screenshot at 2026-01-04 22-00-21" src="https://github.com/user-attachments/assets/47b56902-5133-4a87-808b-2ff06308f83a" />
```
i try common hints endpoint robosts.txt
http://10.80.160.139/robots.txt
You really thought it'd be this easy?
```
i try other path like login,and also admin but amdin return blank page 
### Directory Brute Forcing

After identifying a web service running on port 80, I performed directory brute forcing to discover hidden directories and files that were not directly accessible from the main page.

I used **Gobuster** with a common directory wordlist:

```bash
mrbunny $ gobuster -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt dir -u http://10.80.160.139/ -o directory-disctory.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.80.160.139/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/cgi-bin              (Status: 301) [Size: 315] [--> http://10.80.160.139/cgi-bin/]
/img                  (Status: 301) [Size: 311] [--> http://10.80.160.139/img/]
/uploads              (Status: 301) [Size: 315] [--> http://10.80.160.139/uploads/]
/admin                (Status: 301) [Size: 313] [--> http://10.80.160.139/admin/]
/css                  (Status: 301) [Size: 311] [--> http://10.80.160.139/css/]
/js                   (Status: 301) [Size: 310] [--> http://10.80.160.139/js/]
/backup               (Status: 301) [Size: 314] [--> http://10.80.160.139/backup/]
/secret               (Status: 301) [Size: 314] [--> http://10.80.160.139/secret/]
```




