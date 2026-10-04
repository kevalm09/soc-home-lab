# 🚨 Privileged Group Membership Modified

---

## 📌 Overview

This alert identifies changes to privileged group memberships within the domain, such as users being added to administrative groups.

Such activity is a strong indicator of privilege escalation and potential attacker persistence.

---

## 🔗 Based On Detection

- [privilege escalation](../detections/privilege_escalation.md)

---

## ⚙️ Trigger Logic

This alert is triggered when one or more events are detected indicating a user has been added to a privileged group.

---

## 💻 SPL Query

```spl
index=windows (EventCode=4728 OR EventCode=4732)
| search Group_Name="*Administrators*" OR Group_Name="*Domain Admins*" OR Group_Name="*Remote Desktop Users*"
| table _time, host, Account_Name, Group_Name
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

- **High**

---

## 🧠 Rationale

Modifications to privileged groups are highly sensitive and rarely occur during normal operations without proper authorization.

This alert ensures immediate visibility into potential privilege escalation attempts and unauthorized access control changes.

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name       | Description                   |
| ------------ | -------------------- | ----------------------------- |
| T1098        | Account Manipulation | Modifying account permissions |
