# 🔎 Privileged Group Modification Detection

---

## 📌 Overview

This detection identifies changes to privileged group memberships within the domain, such as adding users to administrative groups.

Attackers often escalate privileges by adding accounts to high-value groups like Domain Admins to gain full control over the environment.

---

## 🧾 Data Source

- **Log Source:** Windows Security Logs
- **Event IDs:**
  - 4728 (Member added to global security group)
  - 4732 (Member added to local security group)
- **Platform:** Windows Domain Controller

---

## 🔍 Detection Logic

This detection monitors for events where users are added to privileged groups within the domain.

It is designed to identify:

- Unauthorized privilege escalation
- Addition of accounts to administrative groups
- Suspicious modification of access control

---

## 💻 SPL Query

```spl
index=windows (EventCode=4728 OR EventCode=4732)
| search Group_Name="*Administrators*" OR Group_Name="*Domain Admins*" OR Group_Name="*Remote Desktop Users*"
| rename Account_Name as Modified_By, Group_Name as Privileged_Group
| table _time, host, Modified_By, Privileged_Group
```

---

## 🧠 How It Works

- Searches for group membership change events
- Filters for high-value or privileged groups
- Identifies the account responsible for the modification
- Displays relevant context for analysis

This enables visibility into privilege escalation activity within the domain.

---

## 🚨 Why It Matters

Privilege escalation is a critical step in gaining full control over a network.

This detection helps:

- Identify unauthorized elevation of privileges
- Detect attacker persistence mechanisms
- Prevent full domain compromise

---

## 🔗 Related Alerts

- privileged_group_alert.md

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name       | Description                   |
| ------------ | -------------------- | ----------------------------- |
| T1098        | Account Manipulation | Modifying account permissions |
