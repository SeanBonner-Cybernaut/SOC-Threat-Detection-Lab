# Wazuh SIEM SOC Analyst Home Lab
**A Complete End-to-End Detection Pipeline Case Study**

## I. Executive Summary
This project demonstrates a fully functional Security Information and Event Management (SIEM) environment built using Wazuh, Windows 10, and Kali Linux.  

The lab simulates a realistic SOC workflow:

-An attacker performs reconnaissance
-Attempts unauthorized authentication
-Creates a persistence account
-Windows logs the activity
-Wazuh ingests, correlates, and alerts
-The analyst investigates and validates detections

This README documents the end‑to‑end detection pipeline, showcasing practical SOC analyst skills.

## II. Lab Architecture  

A three‑machine virtual SOC environment:

- Kali Linux — attacker machine (recon, brute force, enumeration)
- Windows 10 — monitored endpoint (Sysmon + Wazuh agent)
- Wazuh SIEM (Ubuntu 22.04) — centralized log collection, indexing, alerting, and analysis

Kali (Attacker)  

      ↓  
      
Windows 10 (Endpoint → Sysmon → Wazuh Agent)  

      ↓  
      
Wazuh SIEM (Manager + Indexer + Dashboard)

## III. Technology Stack
- Wazuh SIEM 4.14
- vUbuntu Server 22.04 LTS
- Windows 10 Enterprise
- Sysmon + SwiftOnSecurity config
- Kali Linux (Debian 13.x)
- VMware Workstation

## IV. Environment Setup

### Kali Linux

- Installed Debian‑based Kali ISO
- Updated system packages
- Installed VMware tools

### Windows 10 Endpoint 

- Installed Windows 10 ISO  
- Installed Sysmon  
- Installed Wazuh agent  
- Started agent service (net Start wazuh)

### Wazuh SIEM 

- Downloaded and executed Wazuh installation script
- Verified all services (manager, indexer, dashboard, filebeat)
- Confirmed Windows agent connectivity

## V. Log Ingestion Verification

Using Discover → Explore, Windows logs were confirmed to be flowing into Wazuh:

- Security logs
- Sysmon logs
- Authentication events
- System events

The SIEM was fully operational.

## VI. Attack Scenario 1 - Failed Login Attempts (Event ID 4625)
### Objective  
Simulate unauthorized access attempts and validate SIEM detection.  

### Method   
Repeated invalid credential attempts using:    

runas /user:fakeuser cmd

### Detection
Windows generated:

- Event ID 4625 — Failed Logon
- Wazuh assigned Alert Level 5
 -Agent: **Win10‑Lab**

Key fields:

- rule.description: Windows logon failure
- data.win.system.eventID: 4625
- agent.name: Win10-Lab

### Analysis
Multiple failed logons indicate potential brute force or credential‑stuffing behavior.
Even though the user was fake, the authentication subsystem logged it correctly.  

### Outcome  
- Windows logs generated ✔️
- Wazuh agent forwarded logs ✔️
- SIEM detected failed authentication ✔️

## Evidence 
### Event 4625 Wazuh
![Event 4625 Wazuh](Screenshots/Event%204625%20Wazuh.png)

### Event 4625 Eventviewer
![Eventvwr 4625](Screenshots/Eventvwr%204625.png)

### Fakeuser
![Fakeuser](Screenshots/Fakeuser.png)

### Rule Level
![Rule level](Screenshots/Rule%20level.png)

### Wazuh Alerts
![Wazuh Alerts](Screenshots/Wazuh%20Alerts.png)


---

## VII. Attack Scenario 2 - Unauthorized User Creation & Privilege Escalation

### Objective  
Simulate attacker persistence by creating a privileged local account.

### Method  
net user attacker Password123! /add
net localgroup administrators attacker /ad 

Steps:  
1. Opened PowerShell as Administrator
2. Created a new user account:
   net user attacker Password123! /add
3. Added the newly created user to the local Administrators group:
   net localgroup administrators attacker /add

### Detection

Windows generated:

- Event ID 4720 — User Account Created
- Event ID 4732 — User Added to Administrators Group
- Wazuh assigned Alert Level 8

Key fields:

- TargetUserName: attacker
- Group: Administrators
- rule.description: User account created / User added to group

 #### Event ID: 4720 - User Account Created  
 - Indicates a new user account was created on the system  
 - 'TargetUserName': attacker

 #### Event ID: 4732 - User Added to Privileged Group  
 - Indicates a user was added to a security-enabled local group  
 - Group: Administrators  
 - 'MemberName': attacker  

 Additional oobserved fields:  
 - 'rule.description': User account created / User added to group
 - 'rule.level': 8  
 - 'agent.name':Win10-Lab  

 ### Outcome  
 
- Account creation logged ✔️
- Privilege escalation logged ✔️
- Wazuh detected both events ✔️

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
# Wazuh SIEM SOC Analyst Home Lab
*A Complete End-to-End Detection Pipeline Case Study*

---

## 1. Executive Summary

This project demonstrates a fully functional Security Information and Event Management (SIEM) environment built using **Wazuh**, **Windows 10**, and **Kali Linux**.  

The lab simulates a realistic SOC workflow:

- An attacker performs reconnaissance  
- Attempts unauthorized authentication  
- Creates a persistence account  
- Windows logs the activity  
- Wazuh ingests, correlates, and alerts  
- The analyst investigates and validates detections  

This README documents the **end-to-end detection pipeline**, showcasing practical SOC analyst skills.

---

## 2. Lab Architecture

A three-machine virtual SOC environment:

- **Kali Linux** — attacker machine (recon, brute force, enumeration)  
- **Windows 10** — monitored endpoint (Sysmon + Wazuh agent)  
- **Wazuh SIEM (Ubuntu 22.04)** — centralized log collection, indexing, alerting, and analysis  

```text
Kali (Attacker)
      ↓
Windows 10 (Endpoint → Sysmon → Wazuh Agent)
      ↓
Wazuh SIEM (Manager + Indexer + Dashboard)

