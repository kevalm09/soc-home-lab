# 🔎 DNS Reconnaissance Detection

---

## 📌 Overview

This detection identifies suspicious DNS activity associated with internal reconnaissance, including high volumes of queries and repeated failed lookups.

Attackers often use DNS queries to discover internal hosts and services. This may include querying valid hostnames as well as probing for non-existent systems to map the environment.

---

## 🧾 Data Source

- **Log Source:** Zeek DNS Logs
- **Sourcetype:** zeek:dns
- **Platform:** Network (Zeek)

---

## 🔍 Detection Logic

This detection monitors for abnormal DNS query behavior that may indicate reconnaissance activity.

It is designed to identify:

- High volume of DNS queries from a single host
- Repeated failed lookups (`NXDOMAIN`)
- Queries for suspicious or non-existent hostnames

---

## 💻 SPL Query

```spl
index=zeek sourcetype=zeek:dns
| search rcode_name="NXDOMAIN"
| stats count values(query) as Queried_Hosts by src_ip
| where count > 10
```

---

## 🧠 How It Works

- Filters DNS logs for failed lookups (`NXDOMAIN`)
- Groups queries by source IP
- Counts total failed queries
- Triggers when query volume exceeds a defined threshold

This highlights hosts that are performing excessive or suspicious DNS probing.

---

## 🚨 Why It Matters

DNS reconnaissance is a common technique used to map internal environments.

This detection helps:

- Identify early-stage attacker behavior
- Detect internal host discovery attempts
- Correlate with command-based reconnaissance activity

---

## 🔗 Related Alerts

- dns_recon_alert.md

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name            | Description                  |
| ------------ | ------------------------- | ---------------------------- |
| T1046        | Network Service Discovery | Identifying services via DNS |
| T1018        | Remote System Discovery   | Discovering internal hosts   |
