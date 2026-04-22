# 🚨 Suspicious DNS Recon Activity

---

## 📌 Overview

This alert identifies suspicious DNS query behavior indicative of internal reconnaissance and host discovery.

Attackers often use DNS to discover internal systems by querying known, guessed, or non-existent hostnames, resulting in a mix of successful and failed lookups.

---

## 🔗 Based On Detection

- dns_recon_detection.md

---

## ⚙️ Trigger Logic

This alert is triggered when a host generates multiple DNS queries associated with reconnaissance patterns, including failed lookups (`NXDOMAIN`) and suspicious hostname enumeration.

---

## 💻 SPL Query

```spl
index=zeek sourcetype=zeek:dns earliest=-15m
| where NOT match(_raw, "^#")
| rex field=_raw "^(?<ts>[^\t]+)\t(?<uid>[^\t]+)\t(?<src_ip>[^\t]+)\t(?<src_port>[^\t]+)\t(?<dest_ip>[^\t]+)\t(?<dest_port>[^\t]+)\t(?<proto>[^\t]+)\t(?<trans_id>[^\t]+)\t(?<rtt>[^\t]+)\t(?<query>[^\t]+)\t(?<qclass>[^\t]+)\t(?<qclass_name>[^\t]+)\t(?<qtype>[^\t]+)\t(?<qtype_name>[^\t]+)\t(?<rcode>[^\t]+)\t(?<rcode_name>[^\t]+)\t(?<AA>[^\t]+)\t(?<TC>[^\t]+)\t(?<RD>[^\t]+)\t(?<RA>[^\t]+)\t(?<Z>[^\t]+)\t(?<answers>[^\t]+)\t(?<TTLs>[^\t]+)\t(?<rejected>[^\t]+)$"
| search query="*.local" OR query="*dc01*" OR query="*backup*" OR query="*fake*" OR rcode_name="NXDOMAIN"
| where NOT like(query, "%security-onion%")
| stats count values(query) as queried_hosts values(rcode_name) as dns_result by src_ip
| where count >= 3
| rename src_ip as Source_IP, count as Query_Count
| table Source_IP Query_Count queried_hosts dns_result
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

DNS is a low-noise method for attackers to enumerate internal systems.

Repeated queries for both valid and invalid hostnames, especially those resulting in `NXDOMAIN`, indicate probing behavior rather than normal usage.

This alert enables early detection of internal reconnaissance activity before more aggressive actions occur.

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name            | Description                          |
| ------------ | ------------------------- | ------------------------------------ |
| T1046        | Network Service Discovery | Identifying internal systems via DNS |
| T1018        | Remote System Discovery   | Discovering hosts in the environment |
