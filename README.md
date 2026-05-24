## 🛡️ SOC-Lab — Home Security Operations Center 🛡️

A hands-on cybersecurity home lab simulating real world SOC workflows using open source tools.

## 📌 Overview
This project documents the setup and operation of a personal SOC lab built using VirtualBox, Wazuh SIEM, Sysmon and multiple virtual machines.
The goal is to simulate real attack scenarios, detect threat and practice incident response, all in a safe, isolated environment.

## Lab Architecture
```mermaid
flowchart TD
    A[Kali Linux<br>Attacker Machine<br>192.168.56.103]
    B[Wazuh SIEM Server<br>Ubuntu 26.04<br>192.168.56.102]
    C[Windows 11<br>Endpoint 192.168.56.104<br>Sysmon + Wazuh Agent]
    D[Windows Server 2019<br>Endpoint 192.168.56.105<br>Sysmon + Wazuh Agent]
    A -->|Attack Simulation| C
    A -->|Attack Simulation| D
    C -->|Logs & Alerts| B
    D -->|Logs & Alerts| B
```
![Lab Architecture](screenshots/mermaid-diagram.png)
 
## 🖥️ Virtual Machines 
| VM Name | OS | Role | IP Address |
|----------|------------------|------------------------------|----------------|
| Wazuh Server | Ubuntu 26.04 | SIEM & Manager | 192.168.56.102 |
| Attacker | Kali Linux | Red Team | 192.168.56.103 |
| Endpoint 1 | Windows 11 | Monitored Client | 192.168.56.104 |
| Endpoint 2 | Windows Server 2019 | Monitored Server | 192.168.56.105 | 

## Tools & Technologies
| Tool | Purpose |
|------|--------------------------------------------------------------|
| VirtualBox | Virtualization platform  |
| Wazuh | SIEM, IDS, and Log Analysis  |
| Sysmon | Windows event telemetry  |
| Kali Linux | Attack simulation  |
| Nmap | Network scanning  |

## ✅ Progress 
- [x] 4 VMs configured and running
- [x] Host-Only network configured for isolated lab environment
- [x] Wazuh SIEM installed and operational
- [x] SSH configured between Kali and Wazuh Server
- [x] Windows 11 & Windows Server 2019 connected as agents
- [x] Sysmon deployed on Windows endpoints
- [x] First attack simulated: Nmap network scan
- [x] Alerts successfully triggered in Wazuh Dashboard
- [x] Brute Force attack simulated and detected
- [x] SSH remote access simulated and detected
- [x] Attack scenario documentation

## 🔴 Attack Scenarios 

### 1. Nmap Network Scan ✅
**Description:** Simulated a network reconnaissance scan from Kali targeting Windows endpoints.

**Command used:**
```bash
nmap -sV 192.168.56.0/24
```

**Result:**
- Wazuh detected the scan and triggered alerts
- Severity Level 12+ alerts generated
- Sysmon detected suspicious process activity
- MITRE ATT&CK T1046 - Network Service Scanning identified

---

### 2. Targeted Nmap Scan ✅
**Description:** Focused scan on Windows 11 endpoint to identify open ports and OS details.

**Command used:**
```bash
nmap -sS -sV -O 192.168.56.104
```

**Result:**
- Windows 11 detected with filtered ports
- Wazuh generated additional Sysmon alerts
- Defense Evasion and Privilege Escalation tactics identified

---

### 3. Brute Force Attack - SSH ✅
**Description:** Simulated a brute force attack from Kali targeting Windows Server 2019 SSH service.

**Tool used:** Hydra

**Command used:**
```bash
hydra -l Administrator -P /usr/share/wordlists/fasttrack.txt -t 4 -V ssh://192.168.56.105
```

**Result:**
- 29+ Authentication failures detected
- Wazuh triggered alerts automatically
- Tactics detected: Defense Evasion, Persistence, Privilege Escalation, Initial Access
- MITRE ATT&CK T1110 - Brute Force technique

### 4. SSH Remote Access ✅
**Description:** Gained remote access to Windows Server from Kali Linux using SSH credentials obtained from brute force attack.

**Tool used:** SSH

**Command used:**
```bash
ssh Administrator@192.168.56.105
```

**Result:**
- Successfully logged into Windows Server remotely
- Executed commands on the target machine
- Wazuh detected Cleartext logon success
- MITRE ATT&CK T1021.004 - Remote Services: SSH

## 🚀 Getting Started 

### Prerequisites 
- VirtualBox 7.2.6
- Minimum 16GB RAM
- Minimum 200GB free disk space

### Wazuh Installation 
> Run on Ubuntu Server (Wazuh VM)
```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
sudo bash ./wazuh-install.sh -a --ignore-check
```

### Wazuh Agent Installation (Windows)
> Run on Windows PowerShell as Administrator
```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.7.5-1.msi -OutFile ${env.tmp}\wazuh-agent
msiexec.exe /i ${env.tmp}\wazuh-agent /q WAZUH_MANAGER='192.168.56.102' WAZUH_AGENT_NAME='Windows11-SOC'
NET START WazuhSvc
```

### Sysmon Installation 
> Run on Windows PowerShell as Administrator
```powershell
Invoke-WebRequest -Uri https://download.sysinternals.com/files/Sysmon.zip -OutFile C:\Sysmon.zip
Expand-Archive C:\Sysmon.zip -DestinationPath C:\Sysmon
C:\Sysmon\Sysmon64.exe -accepteula -i
```
### Configure Sysmon in Wazuh Agent
> Add to ossec.conf before </ossec_config>
```xml

<localfile>
  <log_format>eventchannel</log_format>
  <location>Microsoft-Windows-Sysmon/Operational</location>
</localfile>

```
> Then restart the service:
```powershell
Stop-Service -Name WazuhSvc -Force
Start-Service -Name WazuhSvc
```

## 📁 Repository Structure

```text
SOC-Lab/
├── README.md
├── screenshots/
│   ├── wazuh-agents.png
│   ├── nmap-scan.png
│   ├── wazuh-dashboard.png
│   ├── windows-server-alerts.png
│   ├── windows11-alerts.png
│   ├── brute-force-dashboard.png
│   ├── brute-force-alerts.png
│   ├── hydra-terminal.png
│   ├── ssh-session.png
│   ├── ssh-commands.png
│   └── ssh-wazuh-alerts.png
├── scenarios/
│   ├── 01-nmap-scan.md
│   ├── 02-targeted-scan.md
│   ├── 03-brute-force.md
│   └── 04-ssh-access.md
└── configs/
    └── ossec-conf-sysmon.xml
```

## 📊 Screenshots 

### 1. Wazuh Agents Active
![Wazuh Agents](screenshots/wazuh-agents.png)

### 2. Nmap Scan Results
![Nmap Scan](screenshots/nmap-scan.png)

### 3. Wazuh Dashboard with Alerts
![Wazuh Dashboard - Windows 11](screenshots/wazuh-dashboard.png)
![Wazuh Dashboard - Windows Server](screenshots/windows-server-alerts.png)

### 4. Security Alerts - MITRE ATT&CK
![Security Alerts](screenshots/windows11-alerts.png)

### 5. Brute Force Attack - Dashboard
![Brute Force Dashboard](screenshots/brute-force-dashboard.png)

### 6. Brute Force Alerts
![Brute Force Alerts](screenshots/brute-force-alerts.png)

### 7. Hydra Terminal
![Hydra Terminal](screenshots/hydra-terminal.png)

### 8. SSH Remote Access Session
![SSH Session](screenshots/ssh-session.png)

### 9. SSH Commands on Windows Server
![SSH Commands](screenshots/ssh-commands.png)

### 10. SSH Wazuh Alerts
![SSH Alerts](screenshots/ssh-wazuh-alerts.png)

## 🎯 Learning Objectives 

- Understand SIEM architecture and log correlation
- Practice threat detection and alert triage
- Simulate real world attack techniques (MITRE ATT&CK)
- Develop incident response procedures
- Configure and manage Windows endpoint monitoring
- Analyze Sysmon logs for suspicious activity
- Understand network segmentation and isolated lab environments
