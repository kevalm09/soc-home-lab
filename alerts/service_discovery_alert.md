# 🚨 Internal Service Discovery / Connection Burst

---

## 📌 Overview

This alert identifies internal service discovery activity characterized by multiple connection attempts to different ports and services on a target system within a short timeframe.

Such behavior is commonly associated with reconnaissance and enumeration techniques used by attackers to identify accessible services for further exploitation.

---

## 🔗 Based On Detection

- service_discovery_detection.md

---

## ⚙️ Trigger Logic

This alert is triggered when an internal host connects to multiple unique ports on another internal system, indicating potential service enumeration.

---

## 💻 SPL Query

```spl
index=zeek sourcetype=zeek:conn earliest=-15m
| where NOT match(_raw, "^#")
| rex field=_raw "^(?<ts>[^\t]+)\t(?<uid>[^\t]+)\t(?<src_ip>[^\t]+)\t(?<src_port>[^\t]+)\t(?<dest_ip>[^\t]+)\t(?<dest_port>[^\t]+)\t(?<proto>[^\t]+)\t(?<service>[^\t]+)\t(?<duration>[^\t]+)\t(?<orig_bytes>[^\t]+)\t(?<resp_bytes>[^\t]+)\t(?<conn_state>[^\t]+)\t(?<local_orig>[^\t]+)\t(?<local_resp>[^\t]+)\t(?<missed_bytes>[^\t]+)\t(?<history>[^\t]+)\t(?<orig_pkts>[^\t]+)\t(?<orig_ip_bytes>[^\t]+)\t(?<resp_pkts>[^\t]+)\t(?<resp_ip_bytes>[^\t]+)\t(?<tunnel_parents>[^\t]+)\t(?<ip_proto>[^\t]+)$"
| search src_ip="10.0.2.*" dest_ip="10.0.1.*"
| stats dc(dest_port) as unique_ports values(dest_port) as ports values(service) as observed_services count as connection_attempts by src_ip, dest_ip
| where unique_ports >= 4
| rename src_ip as Source_IP, dest_ip as Destination_IP, unique_ports as Unique_Ports
| table Source_IP Destination_IP Unique_Ports ports observed_services connection_attempts
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

Internal service discovery is a common precursor to lateral movement, where attackers enumerate available services to identify potential entry points.

The combination of multiple ports, diverse services, and high connection volume within a short timeframe strongly indicates automated or intentional probing behavior.

This alert provides early visibility into reconnaissance activity occurring within the internal network.

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name            | Description                   |
| ------------ | ------------------------- | ----------------------------- |
| T1046        | Network Service Discovery | Enumerating internal services |
