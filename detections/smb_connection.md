# 🔎 SMB Connection Activity Detection

---

## 📌 Overview

This detection provides visibility into SMB connection activity between internal systems, supporting investigation of credential abuse and lateral movement.

It captures both failed and successful SMB connection attempts, allowing analysts to interpret connection outcomes based on connection state values.

---

## 🧾 Data Source

- **Log Source:** Zeek Connection Logs
- **Sourcetype:** zeek:conn
- **Platform:** Network (Zeek)

---

## 🔍 Detection Logic

This detection monitors SMB traffic between internal hosts, focusing on connection attempts to administrative services over port 445.

It is designed to identify:

- Internal SMB connection attempts between hosts
- Repeated access attempts from a single source
- Connection outcomes using `conn_state` values (e.g., failed vs successful)

---

## 💻 SPL Query

```spl
index=zeek sourcetype=zeek:conn earliest=-24h
| where NOT match(_raw, "^#")
| rex field=_raw "^(?<ts>[^\t]+)\t(?<uid>[^\t]+)\t(?<src_ip>[^\t]+)\t(?<src_port>[^\t]+)\t(?<dest_ip>[^\t]+)\t(?<dest_port>[^\t]+)\t(?<proto>[^\t]+)\t(?<service>[^\t]+)\t(?<duration>[^\t]+)\t(?<orig_bytes>[^\t]+)\t(?<resp_bytes>[^\t]+)\t(?<conn_state>[^\t]+)\t(?<local_orig>[^\t]+)\t(?<local_resp>[^\t]+)\t(?<missed_bytes>[^\t]+)\t(?<history>[^\t]+)\t(?<orig_pkts>[^\t]+)\t(?<orig_ip_bytes>[^\t]+)\t(?<resp_pkts>[^\t]+)\t(?<resp_ip_bytes>[^\t]+)\t(?<tunnel_parents>[^\t]+)\t(?<ip_proto>[^\t]+)$"
| search src_ip="10.0.2.*" dest_ip="10.0.1.*" (dest_port=445 OR service="smb")
| sort -_time
| eval _time=strftime(_time,"%m/%d/%Y %I:%M:%S %p")
| table _time src_ip dest_ip dest_port service proto conn_state
```

---

## 🧠 How It Works

- Parses raw Zeek connection logs using `rex`
- Filters for SMB traffic (port 445) between internal systems
- Displays connection attempts along with protocol and connection state
- Enables analysts to determine success or failure based on `conn_state`

---

## 🚨 Why It Matters

SMB is a primary method used for lateral movement in Windows environments.

This detection helps:

- Identify internal access attempts between systems
- Observe patterns of repeated SMB connections
- Distinguish between failed (`RSTO`) and successful (`SF`) access attempts

---

## 🔗 Related Alerts

- smb_activity_alert.md

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name           | Description              |
| ------------ | ------------------------ | ------------------------ |
| T1021.002    | SMB/Windows Admin Shares | Lateral movement via SMB |
