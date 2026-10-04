# 🛡️ Azure SOC Home Lab

## 📌 Overview

This project is a hands-on Security Operations Center (SOC) lab built in **Microsoft Azure** to simulate a multi-stage attack against a Windows Active Directory environment.

The lab combines **endpoint telemetry, authentication events, firewall logs, and network monitoring** into **Splunk Enterprise**, allowing attacker activity to be detected, investigated, and correlated across multiple stages of an attack.

The project focuses on the practical SOC workflow:

**Detection → Investigation → Analysis → Response**

## 📑 Table of Contents

- [🎯 Project Objectives](#-project-objectives)
- [☁️ Lab Environment](#️-lab-environment)
- [🗺️ Architecture](#️-architecture)
- [📥 Telemetry & Log Sources](#-telemetry--log-sources)
- [⚔️ Attack Simulation & Investigations](#️-attack-simulation--investigations)
- [🔎 Detection Engineering](#-detection-engineering)
- [🚨 Alerting](#-alerting)
- [🧭 MITRE ATT&CK](#-mitre-attck)
- [📊 Investigation & Analysis](#-investigation--analysis)
- [🛡️ Incident Response](#️-incident-response)
- [🧠 Skills Demonstrated](#-skills-demonstrated)
- [🗂️ Repository Structure](#️-repository-structure)
- [🚀 Future Improvements](#-future-improvements)
- [⚠️ Disclaimer](#️-disclaimer)
- [🏁 Summary](#-summary)

---

## 🎯 Project Objectives

- Build a segmented enterprise-style environment in Microsoft Azure
- Deploy and configure a Windows Active Directory environment
- Centralize security telemetry in Splunk
- Implement detection logic using Splunk SPL
- Generate and investigate realistic attacker activity
- Correlate endpoint and network telemetry
- Map observed activity to MITRE ATT&CK techniques
- Configure automated alerts for selected detections
- Document investigation and incident response workflows

---

## ☁️ Lab Environment

The lab is hosted in **Microsoft Azure** using a segmented virtual network consisting of three subnets.

### Network Segmentation

| Subnet      | Address Space | Primary Components               |
| ----------- | ------------- | -------------------------------- |
| Workstation | `10.0.2.0/24` | Windows Workstation, pfSense LAN |
| Server      | `10.0.1.0/24` | Domain Controller                |
| Security    | `10.0.3.0/24` | Splunk, Zeek, pfSense WAN        |

### Core Technologies

- **Microsoft Azure** – Cloud infrastructure and virtual networking
- **Windows 11** – Workstation / attacker foothold
- **Windows Server / Active Directory** – Domain services and authentication
- **pfSense** – Firewall, routing, and network logging
- **Zeek** – Network security monitoring
- **Splunk Enterprise** – SIEM, detection, alerting, and visualization
- **Windows Security Logs** – Endpoint and authentication telemetry

---

## 🗺️ Architecture

The environment uses Azure routing and monitoring controls to provide visibility into activity between the workstation, domain controller, and security infrastructure.

Traffic from the workstation subnet is routed through **pfSense**, allowing selected network flows to be inspected and logged.

Azure **vTAP** mirrors traffic from the workstation and domain controller to the Zeek sensor for network analysis.

Security telemetry is then centralized in Splunk.

![SOC Lab Architecture](screenshots/architecture/network_diagram.png)

For a detailed explanation of the network design, routing, traffic monitoring, and log ingestion architecture:

➡️ [View Architecture Documentation](architecture/architecture_overview.md)

---

## 📥 Telemetry & Log Sources

The lab collects telemetry from multiple layers of the environment.

### Windows

Windows Security Event Logs provide visibility into:

- Process creation
- Failed authentication attempts
- User account creation
- Privileged group modifications

Key events include:

- Event ID `4688` – Process Creation
- Event ID `4625` – Failed Logon
- Event ID `4720` – User Account Created
- Event ID `4728` – Member Added to Security-Enabled Global Group

### Zeek

Zeek provides network-level visibility through:

- DNS activity
- Connection metadata
- Source and destination IP addresses
- Destination ports
- Connection states
- Internal service discovery activity

### pfSense

pfSense provides:

- Firewall allow/deny events
- Internal traffic visibility
- Network connection attempts
- Port scanning telemetry

### Splunk

Telemetry is centralized into dedicated indexes:

- `windows`
- `zeek`
- `firewall`

---

# ⚔️ Attack Simulation & Investigations

The lab simulates a multi-stage attack progressing from reconnaissance through persistence.

Each stage is documented as a separate investigation containing attacker activity, evidence, detections, alerts, analysis, and MITRE ATT&CK mapping where applicable.

---

## 1. 🔍 Reconnaissance

The attacker begins by gathering information about the Windows environment and Active Directory domain.

Activities include:

- Identifying the domain
- Discovering the domain controller
- Enumerating users and groups
- Identifying privileged accounts
- Performing DNS-based reconnaissance
- Generating suspicious DNS queries and NXDOMAIN responses

**Key telemetry:**

- Windows Event ID `4688`
- Zeek DNS logs

➡️ [View Reconnaissance Investigation](investigations/01_reconnaissance.md)

---

## 2. ⚠️ Suspicious Execution

The attacker executes commands using PowerShell and legitimate Windows utilities.

Activities include:

- PowerShell execution with suspicious flags
- Execution policy bypass
- Non-profile PowerShell execution
- `certutil.exe` abuse for file transfer

**Key telemetry:**

- Windows Event ID `4688`
- Process command-line data

➡️ [View Suspicious Execution Investigation](investigations/02_suspicious_execution.md)

---

## 3. 🔐 Credential Abuse

The attacker attempts to compromise a privileged domain account identified during reconnaissance.

Activities include:

- Repeated failed authentication attempts
- Targeting the `socadmin` account
- SMB authentication attempts against the domain controller
- Unauthorized internal access attempts blocked by the firewall

**Key telemetry:**

- Windows Event ID `4625`
- Zeek connection logs
- pfSense firewall logs

➡️ [View Credential Abuse Investigation](investigations/04_credential_abuse.md)

---

## 4. 🔎 Internal Discovery

After gaining access to the environment, the attacker performs internal service enumeration against the domain controller.

Activities include probing services such as:

- DNS
- Kerberos
- RPC
- LDAP
- SMB
- RDP
- High-numbered ephemeral ports

The activity generates high-volume, multi-port connection patterns that can be identified through network telemetry.

**Key telemetry:**

- Zeek connection logs
- pfSense firewall logs

➡️ [View Internal Discovery Investigation](investigations/03_internal_discovery.md)

---

## 5. 🚀 Lateral Movement

The attacker uses compromised domain administrator credentials to transition from the compromised workstation user context into the `socadmin` security context.

The attacker uses Windows **Run as different user** to launch Command Prompt with the compromised domain administrator credentials.

The `whoami` command is then used to verify the resulting security context.

**Key evidence:**

- `SOCLAB\socadmin`
- Administrative Command Prompt
- Compromised workstation `10.0.2.4`

This phase demonstrates the use of compromised valid credentials to obtain privileged access.

➡️ [View Lateral Movement Investigation](investigations/05_lateral_movement.md)

---

## 6. 🔒 Persistence

After obtaining administrative access, the attacker establishes persistence by creating a new domain account and adding it to the **Domain Admins** group.

Activities include:

- Creating the `attacker1` domain account
- Adding `attacker1` to Domain Admins
- Establishing an alternate privileged access path

**Key telemetry:**

- Event ID `4720` – Account Creation
- Event ID `4728` – Privileged Group Modification

Separate detections and alerts are used to identify the account creation and subsequent privileged group modification.

➡️ [View Persistence Investigation](investigations/06_persistence.md)

---

# 🔎 Detection Engineering

Detection logic was developed in **Splunk SPL** using telemetry from Windows, Zeek, and pfSense.

Documented detection logic includes:

| Detection                        | Primary Source | Purpose                                             |
| -------------------------------- | -------------- | --------------------------------------------------- |
| Reconnaissance Command Execution | Windows        | Identify enumeration commands                       |
| Suspicious PowerShell Execution  | Windows        | Detect suspicious PowerShell flags                  |
| LOLBin Execution                 | Windows        | Identify abuse of trusted Windows utilities         |
| Failed Logons                    | Windows        | Identify authentication failures                    |
| Brute Force                      | Windows        | Identify repeated failed authentication             |
| Account Creation                 | Windows        | Detect new domain accounts                          |
| Privileged Group Modification    | Windows        | Detect privileged group changes                     |
| Internal Port Scan               | pfSense        | Identify multi-port scanning                        |
| Internal Lateral Movement        | pfSense        | Identify unauthorized internal access attempts      |
| DNS Reconnaissance               | Zeek           | Identify suspicious DNS activity                    |
| Internal Service Discovery       | Zeek           | Identify connection bursts across multiple services |
| SMB Connection Activity          | Zeek           | Provide visibility into internal SMB connections    |

➡️ [View Detection Engineering Documentation](detections/)

---

# 🚨 Alerting

Selected detections were configured as scheduled Splunk alerts. Each alert is documented separately, including its detection logic, trigger conditions, schedule, severity, and MITRE ATT&CK mapping.

### Configured Alerts

| Alert                                                                                 | Description                                         |
| ------------------------------------------------------------------------------------- | --------------------------------------------------- |
| [🚨 Suspicious PowerShell Execution](alerts/sus_powershell_alert.md)                  | Detects suspicious PowerShell execution patterns    |
| [🚨 Potential Brute Force Activity](alerts/brute_force_alert.md)                      | Detects repeated failed authentication attempts     |
| [🚨 Potential Internal Port Scan](alerts/port_scan_alert.md)                          | Detects multi-port internal scanning activity       |
| [🚨 Suspicious DNS Recon Activity](alerts/sus_dns_alert.md)                           | Detects suspicious DNS reconnaissance patterns      |
| [🚨 Internal Service Discovery / Connection Burst](alerts/service_discovery_alert.md) | Detects connection bursts across multiple services  |
| [🚨 New User Account Created](alerts/account_created_alert.md)                        | Detects creation of new domain user accounts        |
| [🚨 Privileged Group Membership Modified](alerts/privileged_group_alert.md)           | Detects modifications to privileged security groups |

---

# 🧭 MITRE ATT&CK

Observed activity is mapped to relevant MITRE ATT&CK techniques throughout the investigation and detection documentation.

Examples include:

| Technique | Technique Name                |
| --------- | ----------------------------- |
| T1087     | Account Discovery             |
| T1018     | Remote System Discovery       |
| T1046     | Network Service Discovery     |
| T1059.001 | PowerShell                    |
| T1218     | Signed Binary Proxy Execution |
| T1110     | Brute Force                   |
| T1078     | Valid Accounts                |
| T1136     | Create Account                |
| T1098     | Account Manipulation          |

The mappings are used to provide context for attacker behavior and connect individual detections to broader adversary techniques.

---

# 📊 Investigation & Analysis

Each investigation follows a consistent SOC analysis workflow:

1. **Attacker Activity**
   - Document the simulated attacker actions

2. **Detection**
   - Identify the telemetry and detection logic that surfaced the activity

3. **Alert**
   - Review the corresponding Splunk alert where applicable

4. **Analysis**
   - Interpret the activity and determine its security significance

5. **MITRE ATT&CK Mapping**
   - Map the observed behavior to relevant techniques

6. **Conclusion**
   - Document the findings and progression of the attack

This structure demonstrates the transition from raw security telemetry to an actionable investigation.

---

# 🛡️ Incident Response

The project also documents incident response and remediation activities covering:

### Containment

- Restricting compromised access
- Terminating active sessions
- Isolating affected systems

### Eradication

- Removing unauthorized accounts
- Reverting privileged group changes
- Clearing active sessions and credentials

### Recovery

- Restoring legitimate administrative access
- Resetting privileged credentials
- Returning systems to a known-good state
- Monitoring for signs of re-compromise

➡️ [View Incident Response Documentation](response/incident_response.md)

---

# 🧠 Skills Demonstrated

This project demonstrates hands-on experience with:

- Splunk Enterprise
- Splunk SPL
- Detection Engineering
- SIEM Alerting
- Windows Security Event Analysis
- Active Directory
- Windows Endpoint Monitoring
- Zeek Network Security Monitoring
- pfSense Firewall Monitoring
- Network Traffic Analysis
- Authentication Analysis
- Incident Investigation
- Incident Response
- MITRE ATT&CK
- Azure Networking
- Cloud-Based Lab Infrastructure
- Multi-source Log Correlation

---

# 🗂️ Repository Structure

```text
soc-home-lab/
│
├── README.md
│
├── architecture/
│   └── architecture_overview.md
│
├── detections/
│   ├── account_creation.md
│   ├── brute_force.md
│   ├── dns_recon.md
│   ├── failed_logons.md
│   ├── internal_lateral_movement.md
│   ├── internal_port_scan.md
│   ├── internal_service_discovery.md
│   ├── lolbin.md
│   ├── privilege_escalation.md
│   ├── recon_command.md
│   ├── smb_connection.md
│   └── suspicious_powershell.md
│
├── alerts/
│   ├── account_created_alert.md
│   ├── brute_force_alert.md
│   ├── port_scan_alert.md
│   ├── privileged_group_alert.md
│   ├── service_discovery_alert.md
│   ├── sus_dns_alert.md
│   └── sus_powershell_alert.md
│
├── investigations/
│   ├── 01_reconnaissance.md
│   ├── 02_suspicious_execution.md
│   ├── 03_internal_discovery.md
│   ├── 04_credential_abuse.md
│   ├── 05_lateral_movement.md
│   └── 06_persistence.md
│
├── response/
│   └── incident_response.md
│
└── screenshots/
    ├── architecture/
    ├── reconnaissance/
    ├── suspicious_execution/
    ├── credential_abuse/
    ├── internal_discovery/
    ├── lateral_movement/
    └── persistence/

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
```
