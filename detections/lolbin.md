# 🔎 LOLBin Execution Detection

---

## 📌 Overview

This detection identifies the execution of legitimate Windows binaries that are commonly abused by attackers to perform malicious actions.

These binaries, known as "Living-Off-the-Land Binaries" (LOLBins), allow attackers to blend in with normal system activity while executing payloads, downloading files, or bypassing security controls.

---

## 🧾 Data Source

- **Log Source:** Windows Security Logs
- **Event ID:** 4688 (Process Creation)
- **Platform:** Windows Endpoint

---

## 🔍 Detection Logic

This detection monitors for execution of commonly abused system binaries associated with malicious activity.

It is designed to identify:

- File downloads using `certutil.exe`
- Script execution via `mshta.exe`
- Background transfers using `bitsadmin.exe`
- Code execution using `rundll32.exe` and `regsvr32.exe`

---

## 💻 SPL Query

```spl
index=windows EventCode=4688 (
    New_Process_Name="*\\certutil.exe" OR
    New_Process_Name="*\\bitsadmin.exe" OR
    New_Process_Name="*\\mshta.exe" OR
    New_Process_Name="*\\rundll32.exe" OR
    New_Process_Name="*\\regsvr32.exe"
)
| table _time, host, Account_Name, New_Process_Name, Process_Command_Line
```

---

## 🧠 How It Works

- Searches process creation logs (Event ID 4688)
- Filters for known LOLBins frequently used in attacks
- Displays execution details including command-line arguments
- Provides visibility into potentially suspicious use of trusted binaries

This enables detection of attacker activity disguised as legitimate system processes.

---

## 🚨 Why It Matters

LOLBins are commonly used to evade detection because they are trusted system binaries.

This detection helps:

- Identify stealthy attacker behavior
- Detect malicious file downloads and execution
- Uncover abuse of legitimate system tools

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name                | Description                    |
| ------------ | ----------------------------- | ------------------------------ |
| T1218        | Signed Binary Proxy Execution | Abuse of trusted binaries      |
| T1105        | Ingress Tool Transfer         | Downloading malicious payloads |
