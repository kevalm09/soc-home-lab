# 🚀 Investigation 5 – Lateral Movement

---

## 📌 Overview

This phase captures the attacker successfully gaining access to the domain controller after previous failed attempts during credential abuse.

Following internal discovery and repeated authentication failures, the attacker is able to authenticate using valid credentials and establish access via SMB.

This behavior is consistent with **lateral movement using valid accounts**, allowing the attacker to pivot from the compromised workstation to a critical system within the domain.

---

## 🧪 Attacker Activity

---

### 📸 Successful SMB Authentication

After multiple failed attempts, the attacker successfully authenticated to the domain controller using valid credentials.

```powershell
net use \\10.0.1.4\C$ /user:soclab\socadmin
```

![SMB Success](../screenshots/lateral_movement/smb_success.png)

**Description:**
The attacker successfully accessed the administrative share (`C$`) on the domain controller, confirming valid credentials and elevated access.

**Key Evidence:**

- **Target System:** `10.0.1.4` (Domain Controller)
- **Access Method:** SMB (port 445)
- **Authentication:** Successful using `socadmin`
- **Result:** Administrative share access established

---

### 📸 Established SMB Session

![SMB Session](../screenshots/lateral_movement/smb_session.png)

**Description:**
The SMB session is successfully established, confirming persistent access to the domain controller.

**Key Evidence:**

- **Connection Status:** Established
- **Remote Share:** `\\10.0.1.4\C$`
- **Session Persistence:** Active connection maintained
- **Privilege Level:** Administrative access

---

## 🔎 Detection

---

### 📸 Successful Logon Detection (Windows Logs)

This detection captures successful authentication events following earlier failed attempts.

🔗 **Detection Details:** [View Detection Logic](../detections/successful_logon_detection.md)

![Successful Logon](../screenshots/lateral_movement/successful_logon_panel.png)

**Key Evidence:**

- **Event ID 4624:** Successful logon
- **Account:** `socadmin`
- **Logon Type:** Network logon (SMB)
- **Target System:** Domain controller (`10.0.1.4`)
- **Time Correlation:** Occurs after multiple failed attempts

---

### 📸 SMB Connection Success (Zeek)

This detection highlights successful SMB connections between the compromised workstation and the domain controller.

🔗 **Detection Details:** [View Detection Logic](../detections/smb_success_detection.md)

![SMB Success Detection](../screenshots/lateral_movement/smb_success_detection.png)

**Key Evidence:**

- **Source → Destination:** `10.0.2.4 → 10.0.1.4`
- **Port:** 445 (SMB)
- **Connection State:** Established / Successful
- **Service:** SMB with authentication
- **Behavior Change:** Transition from failed to successful connections

---

## 🚨 Alert

---

### 📸 Successful Lateral Movement Detected

This alert was triggered based on successful authentication and SMB access to a critical system following prior failed attempts.

🔗 **Alert Logic:** [View Alert Configuration](../alerts/lateral_movement_alert.md)

![Lateral Movement Alert](../screenshots/lateral_movement/lateral_movement_alert.png)

**Key Evidence:**

- **Alert Severity:** High
- **Source Host:** `10.0.2.4`
- **Target Host:** `10.0.1.4`
- **Account Used:** `socadmin`
- **Behavior Pattern:** Successful access following repeated failures
- **Technique:** Lateral movement via valid credentials

---

## 🧠 Analysis

This phase confirms that the attacker has successfully transitioned from attempted access to confirmed compromise of a critical system.

Key indicators include:

- Successful authentication using a privileged domain account
- Access to administrative SMB share (`C$`) on the domain controller
- Transition from failed authentication attempts to successful access
- Network telemetry confirming successful SMB session establishment

This represents a critical escalation in attacker capability, as access to the domain controller provides control over core domain services and user authentication.

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name           | Description                              |
| ------------ | ------------------------ | ---------------------------------------- |
| T1021.002    | SMB/Windows Admin Shares | Lateral movement via SMB shares          |
| T1078        | Valid Accounts           | Use of legitimate credentials for access |

---

## 🏁 Conclusion

The lateral movement phase confirms that the attacker has successfully gained access to the domain controller using valid credentials.

This marks a significant escalation, enabling full control over the domain environment and setting the stage for persistence and further compromise.
