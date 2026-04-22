# 🚨 Potential Brute Force Activity Detected

---

## 📌 Overview

This alert identifies repeated failed authentication attempts from a single source within a short time window, indicating potential brute-force or password spraying activity.

Attackers commonly attempt multiple logins against user accounts to gain access using guessed or compromised credentials.

---

## 🔗 Based On Detection

- brute_force_detection.md

---

## ⚙️ Trigger Logic

This alert is triggered when multiple failed logon attempts occur from the same source within a defined time interval.

---

## 💻 SPL Query

```spl
index=windows EventCode=4625
| bin _time span=5m
| stats count by _time, host, Account_Name, Source_Network_Address
| where count >= 5
| rename Account_Name as User, Source_Network_Address as Source_IP
| sort -count
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

While individual failed logon attempts are common, a high volume of failures within a short timeframe is indicative of automated password guessing or brute-force attacks.

This alert enables early detection of credential abuse attempts before successful authentication occurs.

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name | Description                      |
| ------------ | -------------- | -------------------------------- |
| T1110        | Brute Force    | Repeated authentication attempts |
