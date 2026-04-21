# 🛡️ SOC Analyst Home Lab: Detection, Investigation & Response Project

## 📌 Overview

This project documents the design and implementation of a SOC analyst home lab hosted in a **Microsoft Azure environment**, built to simulate a realistic multi-stage cyber attack in a Windows Active Directory environment.

The lab integrates multiple telemetry sources into **Splunk SIEM**, enabling detection, investigation, and response across:

- Endpoint activity (Windows logs)
- Authentication events (Active Directory)
- Firewall telemetry (pfSense)
- Network visibility (Zeek)

The objective is to demonstrate **end-to-end SOC capabilities**, including detection engineering, log correlation, incident investigation, and response.

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Objectives](#-objectives)
- [Lab Environment](#-lab-environment)
- [Lab Architecture](#-lab-architecture)
- [Investigation Scenario](#️-investigation-scenario-attack-simulation)
- [MITRE ATT&CK Mapping](#-mitre-attck-mapping)
- [Detections Implemented](#-detections-implemented)
- [Alerts Created](#-alerts-created)
- [Incident Response & Remediation](#️-incident-response--remediation)
- [Key Outcomes](#-key-outcomes)
- [Skills Demonstrated](#-skills-demonstrated)
- [Project Structure](#-project-structure)
- [Future Improvements](#-future-improvements)
- [Disclaimer](#️-disclaimer)
- [Summary](#-summary)

---

## 🎯 Objectives

- Build a functional SOC lab in Azure
- Centralize multi-source telemetry into Splunk
- Simulate realistic attacker behavior
- Develop and validate detections
- Investigate attacker activity step-by-step
- Perform containment and remediation actions

---

## ☁️ Lab Environment

### Infrastructure

- **Microsoft Azure** – Hosted lab environment with segmented virtual networking
- **Azure Virtual Network (VNet)** – Simulated internal enterprise network
- **Azure Virtual Machines** – Hosting workstation, domain controller, and monitoring systems

### Core Components

- **Splunk Enterprise** – SIEM, dashboards, detections, alerting
- **Windows 11 Workstation** – Compromised endpoint / attacker foothold
- **Windows Server Domain Controller (DC01)** – Active Directory services
- **pfSense Firewall** – Network segmentation and logging
- **Zeek Network Security Monitor** – DNS and connection telemetry
- **Windows Security Logs** – Endpoint and authentication visibility

---

## 🗺️ Lab Architecture

> Add your network diagram below

![Network Diagram](architecture/network-diagram.png)

This environment simulates internal enterprise communication, authentication flows, and segmented network traffic while providing centralized visibility across multiple telemetry layers.

---

# ⚔️ Investigation Scenario (Attack Simulation)

A multi-stage attack was simulated to validate detection coverage and SOC investigation workflows.

---

## 1. Reconnaissance

- System and domain enumeration (`whoami`, `nltest`, `net user`, `net group`)
- Identification of privileged accounts (Domain Admins)
- Suspicious DNS queries and failed lookups (Zeek)

---

## 2. Suspicious Execution

- PowerShell execution with suspicious flags (`-nop`, `-executionpolicy bypass`)
- Living-off-the-land binary abuse (`certutil.exe`)

---

## 3. Credential Abuse

- Repeated failed logon attempts targeting a privileged account (`socadmin`)
- Brute-force detection via Event ID 4625

---

## 4. Internal Discovery

- Multi-port service discovery against the Domain Controller
- Correlated detection across pfSense and Zeek telemetry

---

## 5. Lateral Movement

- Failed SMB authentication attempts
- Successful SMB access using valid credentials

---

## 6. Access Pivot

- Confirmed access to the Domain Controller via administrative SMB share (`C$`)
- Demonstrates successful use of compromised credentials

---

## 7. Persistence

- Creation of a new domain account (`attacker1`)
- Account added to **Domain Admins**
- Establishes long-term privileged access

---

# 🧭 MITRE ATT&CK Mapping

This mapping demonstrates how observed activity aligns with known adversary techniques, enabling structured detection and response.

| Phase                | Technique ID | Technique Name                | Description                             |
| -------------------- | ------------ | ----------------------------- | --------------------------------------- |
| Reconnaissance       | T1087        | Account Discovery             | Enumerating users and groups            |
| Reconnaissance       | T1018        | Remote System Discovery       | Identifying domain controller           |
| Reconnaissance       | T1046        | Network Service Discovery     | DNS queries and service probing         |
| Execution            | T1059.001    | PowerShell                    | Suspicious PowerShell execution         |
| Execution            | T1218        | Signed Binary Proxy Execution | LOLBin abuse using certutil             |
| Credential Access    | T1110        | Brute Force                   | Repeated failed authentication attempts |
| Discovery            | T1046        | Network Service Discovery     | Port scanning and enumeration           |
| Lateral Movement     | T1021.002    | SMB/Windows Admin Shares      | SMB access attempts                     |
| Lateral Movement     | T1078        | Valid Accounts                | Successful authentication               |
| Persistence          | T1136        | Create Account                | Creation of new domain user             |
| Privilege Escalation | T1098        | Account Manipulation          | Adding user to Domain Admins            |

---

# 🔎 Detections Implemented

| Detection                                     | Data Source  | Description                                |
| --------------------------------------------- | ------------ | ------------------------------------------ |
| Suspicious PowerShell Activity                | Windows Logs | Detect encoded/hidden PowerShell execution |
| Reconnaissance Command Execution              | Windows Logs | Detect enumeration commands                |
| LOLBin Execution                              | Windows Logs | Detect certutil abuse                      |
| Potential Brute Force Activity                | Windows Logs | Detect repeated failed logons              |
| RDP Logons                                    | Windows Logs | Detect remote interactive logons           |
| New User Account Creation                     | Windows Logs | Detect domain account creation             |
| Privileged Group Membership Changes           | Windows Logs | Detect admin group modifications           |
| Internal Port Scan Activity                   | pfSense      | Detect internal service discovery          |
| Internal Lateral Movement Attempts            | pfSense      | Detect SMB-based movement                  |
| Suspicious DNS Recon Activity                 | Zeek         | Detect NXDOMAIN and suspicious queries     |
| Internal Service Discovery / Connection Burst | Zeek         | Detect connection bursts                   |
| Internal SMB Connection Visibility            | Zeek         | Validate SMB lateral movement              |

---

# 🚨 Alerts Created

- Suspicious PowerShell Activity Detected
- Potential Brute Force Activity
- Internal Port Scan Activity Detected
- Suspicious DNS Recon Activity
- Internal Service Discovery / Connection Burst
- New User Account Creation
- Privileged Group Membership Change

---

# 🛡️ Incident Response & Remediation

Following detection of malicious activity, structured response actions were performed.

---

## Containment

- Created a trusted administrative account (`socadmin_clean`)
- Disabled compromised account (`socadmin`)

---

## Eradication

- Removed attacker-created account (`attacker1`)
- Removed privileged group membership

---

## Recovery

- Restored secure administrative access
- Recommended credential resets and audit of privileged accounts

---

# 📈 Key Outcomes

This project demonstrates detection and investigation of:

- Endpoint execution abuse
- Credential-based attacks
- Internal reconnaissance
- Lateral movement behavior
- Privileged account abuse
- Persistence mechanisms

---

# 🧠 Skills Demonstrated

- Splunk SIEM & SPL query development
- Detection Engineering
- Windows Security Event Analysis
- Active Directory Monitoring
- Network Security Monitoring (Zeek)
- Firewall Log Analysis (pfSense)
- Incident Investigation & Response
- Multi-source log correlation
- Cloud fundamentals (Azure environment)

---

# 🗂️ Project Structure

```
SOC-Homelab-Detection-Project/
│
├── README.md
├── dashboards/
├── detections/
├── alerts/
├── investigations/
├── screenshots/
└── architecture/
```

---

# 🚀 Future Improvements

- Add Sysmon for enhanced endpoint visibility
- Expand detections (credential dumping, registry persistence)
- Map detections deeper to MITRE ATT&CK
- Develop response playbooks
- Improve cross-source correlation

---

## ⚠️ Disclaimer

This project was conducted in a controlled lab environment for educational and defensive security purposes only.

---

# 🏁 Summary

This project reflects the full lifecycle of a SOC investigation:

# 👉 Detection → Investigation → Response

It demonstrates the ability to identify malicious activity, analyze attacker behavior, and execute appropriate remediation actions in a simulated enterprise environment.
