# 🔎 Internal Lateral Movement Detection

---

## 📌 Overview

This detection identifies internal connection activity indicative of lateral movement within the network.

After gaining initial access, attackers often attempt to move between systems to expand their control. This detection focuses on identifying repeated internal connection attempts, including those that are blocked or unauthorized.

---

## 🧾 Data Source

- **Log Source:** pfSense Firewall Logs
- **Process:** filterlog
- **Platform:** Network (Firewall)

---

## 🔍 Detection Logic

This detection monitors for internal-to-internal connection attempts from a single host targeting another system within the network.

It is designed to identify:

- Repeated connection attempts to a target host
- Unauthorized or blocked access attempts
- Early-stage lateral movement behavior

---

## 💻 SPL Query

```spl
index=firewall process=filterlog
| rex field=_raw "match,(?<action>\w+),in,\d+,.*?,(?<src_ip>\d+\.\d+\.\d+\.\d+),(?<dest_ip>\d+\.\d+\.\d+\.\d+),(?<src_port>\d+),(?<dest_port>\d+)"
| search src_ip="10.0.2.*" dest_ip="10.0.1.*"
| sort -_time
| table _time, action, src_ip, dest_ip, dest_port
```

---

## 🧠 How It Works

- Extracts relevant fields from firewall logs
- Filters for internal traffic between hosts
- Displays connection attempts including action (allowed, blocked, rejected)
- Provides visibility into internal movement attempts

This enables identification of systems attempting to access other hosts within the environment.

---

## 🚨 Why It Matters

Lateral movement is a key stage in an attack, allowing attackers to expand access beyond the initially compromised system.

This detection helps:

- Identify internal access attempts between systems
- Highlight unauthorized or suspicious connection behavior
- Support investigation of potential lateral movement activity

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name  | Description                     |
| ------------ | --------------- | ------------------------------- |
| T1021        | Remote Services | Lateral movement across systems |
