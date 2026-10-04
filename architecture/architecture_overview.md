# 🏗️ Architecture Overview

---

## 📌 Overview

This lab environment was built in Microsoft Azure to simulate a real-world enterprise network and support detection engineering, alerting, and incident investigation workflows.

The architecture includes segmented networks, controlled traffic flows, and centralized log ingestion into Splunk, enabling visibility across both endpoint and network activity.

---

## ☁️ Azure Environment

### Virtual Network Design

A virtual network was deployed within a dedicated resource group and segmented into three subnets. This segmentation separates user, server, and security infrastructure while enabling controlled traffic flow and centralized monitoring across the environment:

#### 🗄️ Server Subnet (`10.0.1.0/24`)

- Domain Controller

#### 🖥️ Workstation Subnet (`10.0.2.0/24`)

- Windows Workstation
- pfSense LAN interface

#### 🔐 Security Subnet (`10.0.3.0/24`)

- pfSense WAN interface
- Splunk Server
- Zeek Sensor

---

### Routing and Traffic Control

A custom Azure route table was configured to control traffic flow between subnets.

#### 🔹 Key Behavior

- Traffic from the workstation subnet is routed through pfSense
- pfSense acts as the central gateway for outbound traffic
- Internal communication between the workstation and domain controller is routed through pfSense, allowing firewall logging while mirrored traffic is analyzed by Zeek.
- Traffic is routed through pfSense, enabling centralized inspection and logging of selected network flows.

---

#### 🔹 Monitoring Impact

This routing design enables:

- Visibility into lateral movement attempts
- Inspection of outbound connections
- Correlation between network and endpoint activity

---

### 🖥️ Virtual Machine Deployment

All components were deployed as Azure Virtual Machines, with Marketplace images used where applicable to simplify provisioning.

### System Roles

- **Workstation VM**
  - Simulates user activity and attack origin

- **Domain Controller VM**
  - Provides authentication and directory services

- **Splunk VM**
  - Centralized logging, detection, and alerting

- **Zeek VM**
  - Network monitoring and traffic analysis

- **pfSense VM**
  - Firewall and routing control

### 📸 Azure Network Layout

---

![Azure Network](../screenshots/architecture/network_diagram.png)

---

## 🌐 Network Traffic and Monitoring

### Traffic Flow

The environment simulates realistic enterprise communication patterns:

- Workstation → Domain Controller (DNS, Kerberos, LDAP, SMB, RPC)
- Workstation → Internet via pfSense

---

### Traffic Monitoring (vTAP)

Azure vTAP was configured to mirror traffic from:

- Workstation NIC
- Domain Controller NIC

Mirrored traffic is forwarded to the Zeek VM for analysis.

---

### 📸 vTAP Configuration

![vTAP](../screenshots/architecture/vtap.png)

---

## 📥 Log Sources

### Windows Logs

Collected from:

- Workstation
- Domain Controller

Key events:

- Event ID 4688 (Process Creation)
- Event ID 4625 (Failed Logon)
- Event ID 4720 (Account Creation)
- Event ID 4728 (Privileged Group Changes)

---

### Zeek Logs

Generated from mirrored network traffic:

- DNS activity
- Connection logs (ports, services, states)

---

### pfSense Logs

- Firewall allow/deny events
- Internal traffic monitoring
- Port scanning activity

---

## 🔄 Log Ingestion Pipeline

### Data Flow

Logs are centralized into Splunk:

- Windows → Splunk Universal Forwarder → Splunk
- Zeek → Local forwarding → Splunk
- pfSense → Syslog → Splunk

---

### Pipeline Summary

1. Endpoint and network activity is generated within the environment
2. Logs are collected from Windows, Zeek, and pfSense
3. Logs are forwarded and indexed in Splunk
4. Detection logic is applied to identify abnormal patterns
5. Alerts are triggered based on detection conditions

---

### Splunk Data Ingestion

![Splunk Inputs](../screenshots/architecture/splunk_inputs.png)

---

## 🔍 Detection and Alerting

### Detection Layer

Detection logic is implemented in Splunk using SPL queries against Windows, Zeek, and pfSense log sources.

These detections identify behaviors associated with multiple stages of the attack lifecycle, including:

- Reconnaissance
- Suspicious execution
- Service discovery
- Brute-force attempts
- Lateral movement
- Persistence

---

### Alerting Layer

Alerts are configured as scheduled searches:

- Run every 5 minutes
- Trigger when conditions are met
- Assigned severity levels (Medium / High)

---

## 📊 Visualization Layer

Dashboards provide visibility into key activity across endpoint, network, and authentication data.

Panels are built on top of detection queries and highlight patterns such as:

- Host activity (process execution, PowerShell usage)
- Network behavior (DNS queries, connection patterns)
- Authentication trends (failed logons, brute-force attempts)

These visualizations support analysis by enabling correlation across multiple data sources and highlighting abnormal activity patterns.

---

## ⚙️ Configuration Summary

### 🔥 pfSense

#### Firewall Aliases

**AD_Standard_Ports**

- 53, 88, 123, 135, 389, 445, 3389, 49668–49923

---

#### Firewall Rules

1. Allow Authorized AD Traffic
2. Block Unauthorized Lateral Movement
3. Default Allow LAN

---

#### Remote Logging (pfSense → Splunk)

- Server: `10.0.3.4:514`
- Protocol: UDP
- Log Scope: All categories

---

#### 📸 pfSense Logging Configuration

![pfSense Syslog](../screenshots/architecture/pfsense_syslog.png)

---

### 🌐 Zeek

- Receives mirrored traffic via vTAP
- Generates DNS and connection logs
- Forwards logs to Splunk

---

### 📊 Splunk

#### Indexes

- windows
- zeek
- firewall

---

#### Data Sources

- Windows Forwarders
- Zeek logs
- pfSense syslog

---

#### Network Configuration

- Configured to receive Windows Forwarder and syslog data through dedicated listening ports.

---

## 🧠 Summary

This architecture enables:

- Centralized visibility across endpoint and network activity
- Detection of attacker behavior across multiple stages
- Correlation between host and network telemetry
- Automated alerting for rapid response

The integration of Azure networking, pfSense routing, Zeek monitoring, and Splunk analytics provides a complete SOC-style monitoring environment.
