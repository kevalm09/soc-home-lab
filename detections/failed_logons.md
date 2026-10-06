# 🔎 Failed Logon Activity (Time-Based Analysis)

---

## 📌 Overview

This detection provides a time-based view of failed authentication activity across systems, helping identify spikes and patterns associated with potential credential abuse.

Rather than triggering on a fixed threshold, this analysis highlights trends in failed logon attempts over time, supporting the identification of abnormal behavior.

---

## 🧾 Data Source

- **Log Source:** Windows Security Logs
- **Event ID:** 4625 (Failed Logon)
- **Platform:** Windows Endpoint / Domain Controller

---

## 🔍 Detection Logic

This detection visualizes failed logon activity over time to identify anomalies such as spikes in authentication failures.

It is designed to identify:

- Sudden increases in failed logon attempts
- Patterns of repeated authentication failures
- Systems generating abnormal authentication traffic

---

## 💻 SPL Query

```spl
index=windows EventCode=4625
| timechart span=5m count by host
```

---

## 🧠 How It Works

- Collects failed logon events (Event ID 4625)
- Aggregates activity into 5-minute intervals
- Displays failed logon counts over time by host
- Highlights spikes and trends in authentication failures

This provides a visual representation of authentication behavior across the environment.

---

## 🚨 Why It Matters

While individual failed logons may be benign, spikes in failed authentication activity can indicate:

- Brute-force attacks
- Password guessing attempts
- Misconfigured or compromised systems

This detection helps analysts quickly identify abnormal authentication patterns.

---

## 🔗 Related Alerts

- [Brute Force Alert](../alerts/brute_force_alert.md)

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name | Description                      |
| ------------ | -------------- | -------------------------------- |
| T1110        | Brute Force    | Repeated authentication attempts |
