# 🔎 Account Creation Detection

---

## 📌 Overview

This detection identifies the creation of new user accounts within the domain environment.

Attackers often create new accounts after gaining elevated privileges to establish persistence and maintain access even if the original compromised account is removed.

---

## 🧾 Data Source

- **Log Source:** Windows Security Logs
- **Event ID:** 4720 (User Account Created)
- **Platform:** Windows Domain Controller

---

## 🔍 Detection Logic

This detection monitors for events where a new user account is created within the domain.

It is designed to identify:

- Creation of unauthorized or suspicious accounts
- Use of privileged accounts to establish persistence
- Indicators of post-compromise activity

---

## 💻 SPL Query

```spl
index=windows EventCode=4720
| rename Account_Name as Created_By, SAM_Account_Name as New_Account
| table _time, host, Created_By, New_Account
```

---

## 🧠 How It Works

- Searches Windows Security Logs for Event ID 4720
- Extracts the account responsible for creation
- Identifies the newly created user account
- Displays relevant context for investigation

This provides visibility into account creation activity within the domain.

---

## 🚨 Why It Matters

Unauthorized account creation is a strong indicator of compromise and persistence.

This detection helps:

- Identify attacker-created accounts
- Detect persistence mechanisms
- Support rapid containment and remediation

---

## 🔗 Related Alerts

- [Account Created Alert](../alerts/account_created_alert.md)

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name | Description                     |
| ------------ | -------------- | ------------------------------- |
| T1136        | Create Account | Creation of new domain accounts |
