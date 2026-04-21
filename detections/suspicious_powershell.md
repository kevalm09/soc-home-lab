# 🔎 Suspicious PowerShell Execution Detection

---

## 📌 Overview

This detection identifies suspicious PowerShell execution patterns commonly associated with malicious activity, including the use of obfuscation and execution policy bypass techniques.

Attackers frequently use PowerShell with specific flags to evade detection, execute encoded commands, and bypass security controls.

---

## 🧾 Data Source

- **Log Source:** Windows Security Logs
- **Event ID:** 4688 (Process Creation)
- **Platform:** Windows Endpoint

---

## 🔍 Detection Logic

This detection monitors for PowerShell executions that include known suspicious flags often used by attackers.

It is designed to identify:

- Encoded command execution (`-enc`, `-encodedcommand`)
- Execution policy bypass (`-executionpolicy bypass`)
- Hidden or non-interactive execution (`-nop`, `-noprofile`, `-w hidden`)

---

## 💻 SPL Query

```spl
index=windows EventCode=4688 New_Process_Name="*\\powershell.exe"
| rex field=Process_Command_Line "(?i)(?<Suspicious_Flag>-enc|-encodedcommand|-nop|-noprofile|-w hidden|-windowstyle hidden|-executionpolicy bypass)"
| where isnotnull(Suspicious_Flag)
| table _time, host, Account_Name, Suspicious_Flag, Process_Command_Line
```

---

## 🧠 How It Works

- Filters process creation events for PowerShell execution
- Extracts suspicious command-line flags using regex
- Identifies events where one or more suspicious flags are present
- Displays relevant execution details for analysis

This enables detection of stealthy or obfuscated PowerShell activity.

---

## 🚨 Why It Matters

PowerShell is a commonly abused tool for executing malicious payloads and bypassing security controls.

This detection helps:

- Identify potential malware execution
- Detect attempts to evade security monitoring
- Correlate with suspicious process activity and external connections

---

## 🔗 Related Alerts

- suspicious_powershell_alert.md

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name                                | Description                 |
| ------------ | --------------------------------------------- | --------------------------- |
| T1059.001    | Command and Scripting Interpreter: PowerShell | Execution via PowerShell    |
| T1027        | Obfuscated Files or Information               | Encoded or hidden execution |
