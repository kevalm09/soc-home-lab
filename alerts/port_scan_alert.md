# 🚨 Potential Internal Port Scan Detected

---

## 📌 Overview

This alert identifies internal port scanning activity where a single source attempts connections to multiple destination ports on a target system.

Such behavior is commonly associated with reconnaissance, where attackers probe for open services to identify potential entry points.

---

## 🔗 Based On Detection

- internal_port_scan_detection.md

---

## ⚙️ Trigger Logic

This alert is triggered when a source IP connects to multiple unique destination ports on a single internal host within a short timeframe.

---

## 💻 SPL Query

```spl
index=firewall process=filterlog
| rex field=_raw ",(?<src_ip>\d+\.\d+\.\d+\.\d+),(?<dest_ip>\d+\.\d+\.\d+\.\d+),(?<src_port>\d+),(?<dest_port>\d+)"
| stats dc(dest_port) as unique_ports values(dest_port) as ports by src_ip, dest_ip
| where unique_ports >= 4
| sort - unique_ports
```

---

## ⏱️ Schedule

- **Type:** Scheduled
- **Frequency:** Every 5 minutes
- **Time Range:** Last 5 minutes
- **Cron:** _/5 _ \* \* \*

---

## 🚨 Trigger Conditions

- Trigger when **Number of Results > 0**
- Trigger: **Once per search execution**

---

## 🔔 Alert Actions

- Add to Triggered Alerts

---

## 🚨 Severity

- **Medium**

---

## 🧠 Rationale

Port scanning is a common reconnaissance technique used by attackers to identify accessible services on a target system.

The presence of multiple connection attempts across different ports within a short timeframe strongly indicates probing behavior rather than normal application traffic.

This alert provides early detection of internal reconnaissance activity.

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name            | Description                          |
| ------------ | ------------------------- | ------------------------------------ |
| T1046        | Network Service Discovery | Scanning for open ports and services |
