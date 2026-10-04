# 🔎 Internal Port Scan Detection

---

## 📌 Overview

This detection identifies internal port scanning activity originating from a compromised host targeting another system within the network.

Attackers often scan multiple ports on a target system to identify available services and potential entry points for further exploitation.

---

## 🧾 Data Source

- **Log Source:** pfSense Firewall Logs
- **Process:** filterlog
- **Platform:** Network (Firewall)

---

## 🔍 Detection Logic

This detection monitors for connections from a single source to multiple destination ports on a single host within a short timeframe.

It is designed to identify:

- Multiple ports being probed on a single target
- Internal host-to-host scanning behavior
- Early-stage service discovery activity

---

## 💻 SPL Query

```spl
index=firewall process=filterlog
| rex field=_raw ",(?<src_ip>\d+\.\d+\.\d+\.\d+),(?<dest_ip>\d+\.\d+\.\d+\.\d+),(?<src_port>\d+),(?<dest_port>\d+)"
| stats dc(dest_port) as unique_ports values(dest_port) as ports count as connection_attempts by src_ip, dest_ip
| where unique_ports >= 4
```

---

## 🧠 How It Works

- Extracts source and destination IPs and ports from firewall logs
- Counts the number of unique destination ports contacted
- Aggregates total connection attempts
- Triggers when the number of ports exceeds a defined threshold

This allows identification of internal hosts performing multi-port probing behavior.

---

## 🚨 Why It Matters

Port scanning is a common technique used by attackers to identify open services and vulnerabilities.

This detection helps:

- Identify compromised hosts performing reconnaissance
- Detect early-stage attack activity
- Correlate with service discovery and lateral movement attempts

---

## 🔗 Related Alerts

- [Port Scan Alert](../alerts/port_scan_alert.md)

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name            | Description                          |
| ------------ | ------------------------- | ------------------------------------ |
| T1046        | Network Service Discovery | Scanning for open ports and services |
