# 🔎 Internal Service Discovery / Connection Burst Detection

---

## 📌 Overview

This detection identifies high-volume connection activity from a single internal host to multiple services on another internal system.

This behavior is indicative of service enumeration, where an attacker probes multiple ports and services to identify viable paths for lateral movement.

---

## 🧾 Data Source

- **Log Source:** Zeek Connection Logs
- **Sourcetype:** zeek:conn
- **Platform:** Network (Zeek)

---

## 🔍 Detection Logic

This detection monitors for internal hosts that establish connections to multiple ports and services on a single target within a short timeframe.

It is designed to identify:

- High number of connection attempts from one source
- Multiple unique ports being contacted
- Enumeration of common services (DNS, Kerberos, LDAP, SMB, RDP)

---

## 💻 SPL Query

```spl
index=zeek sourcetype=zeek:conn
| search src_ip="10.0.2.*" dest_ip="10.0.1.*"
| stats dc(dest_port) as unique_ports values(dest_port) as ports values(service) as services count as connection_attempts by src_ip, dest_ip
| where unique_ports >= 4
```

---

## 🧠 How It Works

- Analyzes network connection logs from Zeek
- Groups connections by source and destination IP
- Counts unique destination ports contacted
- Aggregates total connection attempts and observed services
- Triggers when multiple services are probed on a single host

This allows identification of systematic service enumeration activity.

---

## 🚨 Why It Matters

Service discovery is a critical step attackers take before attempting lateral movement.

This detection helps:

- Identify internal reconnaissance activity
- Detect compromised hosts performing enumeration
- Correlate with port scanning and credential abuse attempts

---

## 🔗 Related Alerts

- [Service Discovery Alert](../alerts/service_discovery_alert.md)

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name            | Description               |
| ------------ | ------------------------- | ------------------------- |
| T1046        | Network Service Discovery | Probing internal services |
