# SOC-Threat-Detection-Lab
A SOC Analyst Lab to simulate cyber-attacks and detect them using a SIEM platform. The environment used in this lab includes Kali Linux (attacker), Windows 10 (Victim), and Ubuntu running the Wazuh SIEM.
## Choosing Your Hypervisor
I decided to use [VMware Workstation Pro 25H2 for Windows](https://support.broadcom.com/group/ecx/productdownloads?subfamily=VMware%20Workstation%20Pro&freeDownloads=true) for this project due to its efficient use of CPU and RAM virtualization.  
Prior to file installation I checked the file's SHA256 hash using the certutil -hashfile command in Command Prompt to ensure that the file had not been currupted.  
Upon running the certutil command I saw that the [file hash]("C:\Users\Laidi\OneDrive\Desktop\SOC-Analyst-Lab\Screenshots\VMware Workstation Pro Hash Functions.png") that I received was the same as that posted on the offical website, so I proceeded with installation.
