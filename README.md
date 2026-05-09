## 🛡️ SOC-Lab — Home Security Operations Center 🛡️

A hands-on cybersecurity home lab simulating real world SOC workflows using open source tools.

## 📌 Overview
This project documents the setup and operation of a personal SOC lab built using VirtualBox, Wazuh SIEM, Sysmon and multiple virtual machines.
The goal is to simulate real attack scenarios, detect threat and practice incident response, all in a safe, isolated environment.

## Lab Architecture
```mermaid
flowchart TD

    A[Kali Linux<br>Attacker Machine]

    B[Wazuh SIEM Server<br>Ubuntu 22.04<br>192.168.56.102]

    C[Windows 11<br>Endpoint<br>Sysmon + Wazuh Agent]

    D[Windows Server<br>Endpoint<br>Sysmon + Wazuh Agent]

    A -->|Attack Simulation| C
    A -->|Attack Simulation| D

    C -->|Logs & Alerts| B
    D -->|Logs & Alerts| B
```
 
