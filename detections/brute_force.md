# 🔎 Brute Force Detection

---

## 📌 Overview

This detection identifies patterns of repeated failed authentication attempts that indicate potential brute-force or password guessing activity.

It builds on failed logon events to detect abnormal authentication behavior targeting specific accounts within a short timeframe.

---

## 🧾 Data Source

- **Log Source:** Windows Security Logs
- **Event ID:** 4625 (Failed Logon)
- **Platform:** Windows Endpoint / Domain Controller

---

## 🔍 Detection Logic

This detection triggers when a high number of failed logon attempts are observed against one or more accounts within a defined time window.

It is designed to identify:

- Repeated authentication attempts targeting a single account (e.g., `socadmin`)
- Multiple failed attempts originating from the same source host
- Rapid succession of failed logons indicating automated behavior

---

## 💻 SPL Query

```spl
index=windows EventCode=4625
| bin _time span=1m
| stats count by _time, Account_Name, Source_Network_Address
| where count > 10
```

---

## 🧠 How It Works

- Collects failed logon events (Event ID 4625)
- Groups events into 1-minute intervals
- Counts the number of failed attempts per user and source IP
- Triggers when failures exceed a defined threshold

This allows detection of abnormal authentication spikes that would not occur during normal user behavior.

---

## 🚨 Why It Matters

Brute-force activity is a common method used by attackers to gain access to privileged accounts.

This detection helps identify:

- Password guessing attempts
- Automated attack behavior
- Early-stage credential compromise

Early detection can prevent attackers from successfully authenticating and moving laterally within the environment.

---

## 🔗 Related Alerts

- [Brute Force Alert](../alerts/brute_force_alert.md)

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name | Description                      |
| ------------ | -------------- | -------------------------------- |
| T1110        | Brute Force    | Repeated authentication attempts |
