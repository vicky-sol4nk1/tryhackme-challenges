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

