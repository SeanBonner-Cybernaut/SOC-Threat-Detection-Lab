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

