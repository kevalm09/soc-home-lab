# 🔎 Reconnaissance Command Execution Detection

---

## 📌 Overview

This detection identifies command-line activity associated with system and domain reconnaissance using native Windows utilities.

Attackers often use built-in commands to gather information about the environment, including system details, user accounts, and domain structure. This behavior is commonly referred to as "living off the land."

---

## 🧾 Data Source

- **Log Source:** Windows Security Logs
- **Event ID:** 4688 (Process Creation)
- **Platform:** Windows Endpoint

---

## 🔍 Detection Logic

This detection monitors for execution of common reconnaissance commands that are frequently used during the early stages of an attack.

It is designed to identify:

- System enumeration (`whoami`, `systeminfo`)
- Network configuration discovery (`ipconfig`)
- Domain controller discovery (`nltest`)
- User and group enumeration (`net user`, `net group`, `net localgroup`)

---

## 💻 SPL Query

```spl
index=windows EventCode=4688
(
    Process_Command_Line="*whoami*"
    OR Process_Command_Line="*ipconfig*"
    OR Process_Command_Line="*systeminfo*"
    OR Process_Command_Line="*nltest*"
    OR Process_Command_Line="*net user*"
    OR Process_Command_Line="*net.exe* user*"
    OR Process_Command_Line="*net group*"
    OR Process_Command_Line="*net.exe* group*"
    OR Process_Command_Line="*net localgroup*"
    OR Process_Command_Line="*net.exe* localgroup*"
)
| eval Recon_Command=Process_Command_Line
| table _time, host, Account_Name, Recon_Command
```

---

## 🧠 How It Works

- Searches for process creation events (Event ID 4688)
- Filters for known reconnaissance-related commands
- Extracts and displays command-line activity
- Highlights execution patterns associated with enumeration

This allows visibility into attacker behavior using legitimate system tools.

---

## 🚨 Why It Matters

Reconnaissance activity is often the first step after initial access.

Detecting this behavior helps:

- Identify compromised hosts early
- Understand attacker intent
- Prevent further progression into credential abuse and lateral movement

---

## 🔗 Related Alerts

- recon_activity_alert.md

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name               | Description                   |
| ------------ | ---------------------------- | ----------------------------- |
| T1087        | Account Discovery            | Enumerating users and groups  |
| T1018        | Remote System Discovery      | Identifying domain controller |
| T1082        | System Information Discovery | Gathering host details        |
