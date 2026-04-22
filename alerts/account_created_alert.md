# 🚨 New User Account Created

---

## 📌 Overview

This alert identifies the creation of new user accounts within the environment, which may indicate unauthorized persistence or account abuse following a compromise.

Attackers often create new accounts to maintain access after gaining elevated privileges.

---

## 🔗 Based On Detection

- account_creation_detection.md

---

## ⚙️ Trigger Logic

This alert is triggered when a new user account is created, as indicated by Windows Security Event ID 4720.

---

## 💻 SPL Query

```spl
index=windows EventCode=4720
| rename Account_Name as Created_By, SAM_Account_Name as New_Account
| table _time, host, Created_By, New_Account
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

The creation of new user accounts is a sensitive operation that can indicate persistence or unauthorized access if not properly validated.

Monitoring this activity ensures visibility into potential attacker behavior, especially when new accounts are created outside of normal administrative processes.

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name | Description                              |
| ------------ | -------------- | ---------------------------------------- |
| T1136        | Create Account | Creation of new accounts for persistence |
