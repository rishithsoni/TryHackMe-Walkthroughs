
# TryHackMe: RootMe Write-up 🚩
**Machine Name:** RootMe  
**Difficulty:** Easy  
**Platform:** TryHackMe  
**Objective:** Gain root access to the Linux machine.

⚠️Disclaimer: This walkthrough is for educational purposes only. The IP address used in this documentation (10.49.178.211) was specific to my lab session. If you are following along, your target IP address will be different. Do not copy my IP; use the one provided by TryHackMe when you deploy your machine.

Step 0: Lab Setup & Connectivity
To begin the RootMe challenge, I deployed the target machine on TryHackMe. Instead of using the browser-based AttackBox, I chose to use my local Kali Linux VM for a more realistic environment. I connected to the TryHackMe network using OpenVPN.

Command: sudo openvpn your_config.ovpn

<img width="581" height="82" alt="Screenshot 2026-01-08 150934" src="https://github.com/user-attachments/assets/8c4985b7-9bea-4a98-bd86-2bd2751721e8" />

I checked for the "Initialization Sequence Completed" message to ensure the tunnel was active.

Identifying My Target: The target machine was deployed at 10.49.178.211. I confirmed connectivity by sending a simple ping:

Command: ping -c 3 10.49.178.211

Step 1: Reconnaissance (Nmap)

I started with an Nmap scan to find open ports and service versions. This helps identify the 'attack surface.

Command: nmap -sV <Target_IP>

<img width="632" height="331" alt="Screenshot 2026-01-08 151130" src="https://github.com/user-attachments/assets/0d668efb-49d5-4bff-8b57-ad1482549b19" />

Step 2: Directory Discovery (Gobuster)

The Nmap scan revealed a web server on Port 80. Since the homepage appeared standard, I used Gobuster to perform a directory brute-force attack. This allowed me to find hidden paths on the server that aren't linked on the main page.

Command: gobuster dir -u http://10.49.178.211 -w /usr/share/wordlists/dirb/common.txt

<img width="880" height="446" alt="Screenshot 2026-01-08 151213" src="https://github.com/user-attachments/assets/aaa31907-e668-455f-8647-451528bbc552" />

I discovered  directory: /p****/

Step 3: Gaining Access (Reverse Shell)

Upon navigating to the hidden directory, I found a file upload form. I attempted to upload a standard PHP reverse shell, but it was blocked. By renaming the file extension to .phtml, I successfully bypassed the filter. I then set up a Netcat listener on my local machine to 'catch' the incoming connection.


Commands:


nc -lvnp 9999 (Your listener)
python -c 'import pty; pty.spawn("/bin/bash")' (To stabilize your shell)

Once the connection was established, the shell was a bit 'unstable' (no autocomplete, no clear prompt). I used the Python pty module to upgrade to a more stable bash shell.


Step 4: Uploading shell.pthml file 
After turning on the listner go in your browesr and search http://<target.ip>/p****/

<img width="1107" height="646" alt="Screenshot 2026-01-08 151332" src="https://github.com/user-attachments/assets/3510d815-7cb5-4bc7-bdff-02e96aa3e02e" />


Then select the file shell.phtml and upload it 

<img width="913" height="593" alt="Screenshot 2026-01-08 151406" src="https://github.com/user-attachments/assets/0583408f-a00c-4da3-8556-774b23cec977" />



###BUT BEFORE UPLOADING THE FILE OPEN THAT FILE USING COMMAND "nano shell.phtml" AND CHANGE IP TO YOUR AND PORT OF YOUR CHOICE###


<img width="395" height="336" alt="Screenshot 2026-01-08 151432" src="https://github.com/user-attachments/assets/5389147b-cbe6-4c75-9c36-3f1aa204c035" />

Now, after uploading go to http://<target.ip>/uploads/

and click on sheel.pthml 
and look to you lisner 


<img width="918" height="712" alt="Screenshot 2026-01-08 151541" src="https://github.com/user-attachments/assets/2fb25863-bd79-40eb-901a-dae4757c4ad1" />

STEP 5: Privilege Escalation & Final Flags
After gaining an initial shell as www-data, the final goal was to escalate privileges to the root user.

1. Identifying the Vulnerability (SUID Search): I searched the system for files with the SUID (Set User ID) bit enabled. This bit allows a file to be executed with the permissions of the owner (in this case, root).

Command: find / -user root -perm -4000 -print 2>/dev/null

Discovery: The scan revealed that /usr/bin/python had the SUID bit set. This is a critical security risk because Python can be used to execute system commands.

2. Exploiting the SUID Bit Using a technique from GTFOBins: I executed a Python one-liner to spawn a shell with root privileges (-p flag).

Command: python -c 'import os; os.execl("/bin/sh", "sh", "-p")'

Verification: I ran whoami, and the system returned root.

3. Capturing the Flags Now with full administrative access, I retrieved both the user and root flags.

User Flag: Located at /var/www/user.txt

THM{********************}

Root Flag: Located at /root/root.txt
THM{********************}

<img width="466" height="271" alt="Screenshot 2026-01-08 151601" src="https://github.com/user-attachments/assets/107983d5-4095-4afc-b8bc-f656d387831c" />

<img width="498" height="64" alt="Screenshot 2026-01-08 151618" src="https://github.com/user-attachments/assets/5261f9c5-4bd2-4692-8b53-769455541001" />

<img width="401" height="83" alt="Screenshot 2026-01-08 152044" src="https://github.com/user-attachments/assets/9ae19ac1-a908-4ce9-99f1-f6258993fca3" />





