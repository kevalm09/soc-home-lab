# 🚨 Suspicious PowerShell Execution Detected

---

## 📌 Overview

This alert identifies PowerShell executions that include suspicious command-line flags commonly associated with malicious activity.

Attackers frequently use PowerShell with obfuscation and execution bypass techniques to evade detection and execute payloads.

---

## 🔗 Based On Detection

- suspicious_powershell_detection.md

---

## ⚙️ Trigger Logic

This alert is triggered when PowerShell is executed with known suspicious flags indicative of obfuscation or stealthy execution.

---

## 💻 SPL Query

```spl
index=windows EventCode=4688 New_Process_Name="*\\powershell.exe"
| rex field=Process_Command_Line "(?i)(?<Suspicious_Flag>-enc|-encodedcommand|-nop|-noprofile|-w hidden|-windowstyle hidden|-executionpolicy bypass)"
| where isnotnull(Suspicious_Flag)
| table _time, host, Account_Name, Suspicious_Flag, Process_Command_Line
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

PowerShell is a commonly abused tool in attacks due to its flexibility and ability to execute code directly in memory.

The presence of flags such as `-enc`, `-nop`, and `-executionpolicy bypass` strongly indicates attempts to evade detection and execute malicious commands.

This alert ensures rapid visibility into potentially malicious PowerShell activity.

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name                                | Description                 |
| ------------ | --------------------------------------------- | --------------------------- |
| T1059.001    | Command and Scripting Interpreter: PowerShell | Execution via PowerShell    |
| T1027        | Obfuscated Files or Information               | Encoded or hidden execution |
