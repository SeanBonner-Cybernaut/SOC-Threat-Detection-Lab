# Wazuh SIEM SOC Analyst Home Lab

## Overview
This project demonstrates the design and deployment of a Security Information and Event Management (SIEM) solution using Wazuh. The Lab simulates a real-world SOC environment where endpoint activity is monitored, security events are collected, and alerts are analyzed.

## Objective
The objective of this lab is to:  
-Deploy a fully functional SIEM using Wazuh.  
-Monitor endpoint activity from a Windows 10 machine.  
-Analyze security events and logs.  
-Build hands-on experience with SOC Analyst workflow.

## Lab Achitecture
  **Kali Linux**: Attack simulation machine  
  **Windows 10**: Monitored endpoint (Wazuh agent installed)  
  **Wazuh SIEM**: Centralized logging, detection, and visualization 

## Technology Used
-Wazuh SIEM (Ubuntu 22.04 LTS Server)  
-Kali Linux (Attack Simulation)  
-Windows 10 (Endpoint with Wazuh Agent)  
-Linux Command Line-Powershell-VMware

## Installation & Setup

### Kali Linux Setup
-Download the Debian 13.x 64-bit iso from https://www.kali.org/get-kali/#kali-platforms.  
-In VMware create new machine.  
-After installation run sudo apt update && sudo apt upgrade -y to apply any updates.  
-Run sudo apt install open-vm-tools open-vm-tools-desktop -y to install VMware tools

### Windows 10 Setup
-Download Windows 10 iso from the offical Microsoft website https://www.microsoft.com/en-us/software-download/windows10?msockid=28f471f593d16ad6311866e992b66b11.  
-Create a new machine in VMware.-Install Sysmon by running sysmon64.eexe -accepteula -i sysmonconfig.xml in Powershell.  
-Download Windows agent from Wazuh dashboard.  
-Run net start wazuh in Powershell.

### Wazuh SIEM (Ubuntu Server 22.04 LTS)
-Downloaded the Wazuh installation script:  
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh  
-Made the script executable:  
--chmod +x wazuh-install.sh  
-Ran the installation:  
--sudo bash wazuh-install.sh  
-Verified services:  
--sudo systemctl status wazuh-manager  
--sudo systemctl status wazuh-indexer  
--sudo systemctl status wazuh-dashboard  
--sudo systemctl status filebeat

## Log Ingestion Verification
-Navigated to:  
--Explore  
---Discover  
Confirmed that logs were being ingested from the Windows agent.

## Current Status
The SIEM environment is fully operational:  
-Wazuh Manager, Indexer, Dashboard, and Filebeat are running.  
-Windows agent successfully connected.  
-Logs are being ingested and analyzed.

---

## Attack Simulation: Failed Login Attempts
### Objective  
Simulate unauthorized access attemps against a Windows 10 endpoint and verify detection within the Wazuh SIEM.

---

### Method
Failed login attempts were generated using the Windows 'runas' command with invalid credentials.  

Steps:  
1. Opened Powershell (Run as Administrator)
2. Executed the following command:

   runas /user:fakeuser cmd

3. Entered an incorrect password when prompted
4. Repeated the process three times to simulate multiple failed login attempts

---

### Detection
The activity generated Windows Security Event Logs, which were successfully ingested by Wazuh.  

-**Event ID:** 4625 (Failed Logon)  
-**Wazuh Alert Level:** 5 (Medium severity)  
-**Agent:** Windows 10 endpoint  

Key fields observed:  
- 'rule.description': Windows logon failure
- 'rule.level': 5
- 'agent name': Win10-Lab
- 'data.win.system.eventID': 4625
  
---

### Analysis
The repeated failed login attempts indicate potential unauthorized access activity. While the attempts used a non-existent user account, they still triggered authentication failure events  with Windows.

---

### Outcome
This simulation confirmed that:  
-Windows Security logs are being generated correctly  
-The Wazuh agnet is successfully collecting and forwarding logs  
-Failed authentication attempts are detectable within the SIEM  

---

### Evidence

#### Event 4625 Wazuh
![Event 4625 Wazuh](Screenshots/Event%204625%20Wazuh.png)

#### Event 4625 Eventviewer
![Eventvwr 4625](Screenshots/Eventvwr%204625.png)

#### Fakeuser
![Fakeuser](Screenshots/Fakeuser.png)

#### Rule Level
![Rule level](Screenshots/Rule%20level.png)

#### Wazuh Alerts
![Wazuh Alerts](Screenshots/Wazuh%20Alerts.png)
-Event Viewer showing Event ID 4625  
-Wazuh Discovery view showing corresponding alerts  
-Expanded log details with event fields

---

## Attack Simulation: Unauthorized User Creation & Privilege Escalation

### Objective  
Simulate attacker persistence by creating a new user account and elevating its privileges on a Windows 10 endpoint, and verify detection within the Wazuh SIEM.

---

### Method  
User account creation and privilege escalation were performed using built-in Windows commands.  

Steps:  
1. Opened PowerShell as Administrator
2. Created a new user account:
   net user attacker Password123! /add
3. Added the newly created user to the local Administrators group:
   net localgroup administrators attacker /add
   
---

### Detection

The actions generated Windows Security Event Logs, which were successfully ingested by Wazuh.  

 #### Event ID: 4720 - User Account Created  
 -Indicates a new user account was created on the system  
 -'TargetUserName': attacker

 #### Event ID: 4732 - User Added to Privileged Group  
 -Indicates a user was added to a security-enabled local group  
 -Group: Administrators  
 -'MemberName': attacker  

 Additional oobserved fields:  
 -'rule.description': User account created / User added to group
 -'rule.level': 8  
 -'agent.name':Win10-Lab  

 ---

 ### Outcome  
 This simulation confirmed that:  
 -Account creation and privilege escalation events are logged by Windows  
 -Wazuh successfully detects and ingest these events  
 -Privileged account changes are visible and can be monitored for suspicious activity  

 ---  

### Evidence  

#### Attacker to Administrator
![Attacker to Administrator_add](Screenshots/Attacker%20to%20Administrator_add.png)

#### Event Viewer – 4720 User Created
![Event Viewer 4720 User Created](Screenshots/Event%20Viewer%204720%20User%20Created.png)

#### Event Viewer – 4732
![Event Viewer 4732](Screenshots/Event%20Viewer%204732.png)

#### New User Created
![New User Created](Screenshots/New%20User%20Created.png)

#### Wazuh Event Log 2
![Wazuh Event Log 2](Screenshots/Wazuh%20Event%20Log%202.png)

#### Wazuh Event Log 3
![Wazuh Event Log 3](Screenshots/Wazuh%20Event%20Log%203.png)

#### Wazuh Event Log
![Wazuh Event Log](Screenshots/Wazuh%20Event%20Log.png)

#### Wazuh Rule Level
![Wazuh rule.level](Screenshots/Wazuh%20rule.level.png)

## Network & Device Reconnaisance

### Overview
This simulation is a controlled network reconnaissance exercise performed in a virtual SOC environment. The goal was to identify active hosts, assess network visibility, and evaluate exposed services on a Windows endpoint from a Kali attacker machine. The exercise demonstrates foundational SOC and penetration testing skills, focusing on **asset discovery, service enumeration, and firewall behavior analysis** within an isolated lab environment. 

---

### Environment
-**Windows 10 Endpoint**
  -Target system
  -Security logging enabled (Windows Event Logs)
-**Kali Linux Attacker Machine**
  -Used for reconnaissance and network discovery
  -Tools: Nmaps, ICMP utilities
-**Wazuh SIEM (Ubuntu Server)**
  -Centralized log collection and security monitoring
  -used for validating detection of authenticaiton-related activity (where applicable)

 ---

 ### Objective
 -Identify active hosts on the local subnet
 -Validate network connectivity between attacker and target systems
 -Perform TCP service discovery on the target endpoint
 -Asses firewall filtering behavior and exposed services
 -Establish baseline visibility for future attack simulation phases

 ---

 ### Reconnaissance Activities Performed

 #### Determining Network
 -Use ip a command to identify the network.

 #### Host Discovery (ICMP Ping)
 Initial connectivity testing was performed to confirm the presence of the Windows endpoint.  

 -Tool: Nmap host discovery scan.  
 -Result: Windows host identified.

 #### TCP Port Scanning
-A full TCP Scan was performed against the Windows endpoint to evaluate exposed services.  

-Result: All scanned TCP ports were reported as **filtered**  
  -Windows Firewall is actively blocking inbound connection attempts
  -No externally visible servises detected (e.g. RDP, SMB)

  ---   

  ### Services Exposure Analysis
  Further validation confirmed that no standard Windows services were accessible externally from the scanning host. 

  -No open ports identified  
  -No services enumeration possible  
  -Strong host-based firewall posture observed  

  ---  

  ### Key Findings
  -The Windows endpoint is reachable at the network layer (ICMP allowed)  
  -All TCP traffic is being filtered before reaching services  
  -No External exposed services were identified during initial reconnaissance  
  -Firewall configuration significantly reduces attack surface visibility  

  ---  

  ### Security Interpretation  
  This lab demonstrates a realistic enterprise-style defensive posture where:  

  -Hosts are discoverable but not directly exploitable  
  -Network reconnaissance yields limited exposure  
  -Firewall rules effectively prevent service enumeration  
  -Attack surface visibility is minimized at the perimeter  

  This scenario reflects a common real-world environment where attackers must progress beyond basic scanning to identify viable entry points.

  ### Evidence
#### Determine the network the device is on  

![Kali Device ID_ip a](Screenshots/Kali%20Device%20ID_ip%20a.png)  

#### Network Device Discovery

![Network Device Discovery_ip a](Screenshots/Network%20Device%20Discovery_ip%20a.png)

#### Password Cracking Attempt

![Hydra](Screenshots/Hydra.png)

#### No Event Code
![No event code](Screenshots/No%20event%20code.png)  

#### Port Reconnaissaince

![Port Reconnaissance](Screenshots/Port%20Reconnaissance.png)
 
## Author
Sean M. Bonner

