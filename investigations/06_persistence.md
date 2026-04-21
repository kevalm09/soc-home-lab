# 🔒 Investigation 6 – Persistence

---

## 📌 Overview

This phase captures actions taken by the attacker to maintain long-term access within the environment after successfully compromising the domain controller.

Following lateral movement and administrative access, the attacker establishes persistence by creating a new privileged account within the domain.

This behavior ensures continued access even if the original compromised credentials are detected or disabled.

---

## 🧪 Attacker Activity

---

### 📸 Account Creation

The attacker creates a new domain account to maintain access.

```powershell
net user socadmin_clean Password123! /add /domain
```

![Account Creation](../screenshots/persistence/account_creation.png)

**Description:**
A new domain user account is created to establish persistent access within the environment.

**Key Evidence:**

- **New Account:** `socadmin_clean`
- **Scope:** Domain account
- **Action:** Account creation using administrative privileges
- **Purpose:** Establish alternate access path

---

### 📸 Privilege Escalation (Account Added to Domain Admins)

The attacker elevates the newly created account to a privileged role.

```powershell
net group "Domain Admins" socadmin_clean /add /domain
```

![Privilege Escalation](../screenshots/persistence/privilege_escalation.png)

**Description:**
The attacker adds the newly created account to the Domain Admins group, granting full administrative privileges.

**Key Evidence:**

- **Account:** `socadmin_clean`
- **Group:** Domain Admins
- **Privilege Level:** Full administrative access
- **Impact:** Persistent privileged access established

---

## 🔎 Detection

---

### 📸 Account Creation Detection (Windows Logs)

This detection captures the creation of new user accounts within the domain.

🔗 **Detection Details:** [View Detection Logic](../detections/account_creation_detection.md)

![Account Creation Detection](../screenshots/persistence/account_creation_detection.png)

**Key Evidence:**

- **Event ID 4720:** User account created
- **New Account:** `socadmin_clean`
- **Created By:** Privileged account
- **Target System:** Domain controller

---

### 📸 Privileged Group Modification Detection

This detection identifies changes to privileged groups within the domain.

🔗 **Detection Details:** [View Detection Logic](../detections/group_modification_detection.md)

![Group Modification Detection](../screenshots/persistence/group_modification_detection.png)

**Key Evidence:**

- **Event ID 4728:** Member added to security-enabled global group
- **Group:** Domain Admins
- **Added Account:** `socadmin_clean`
- **Privilege Escalation:** Confirmed

---

## 🚨 Alert

---

### 📸 Privileged Account Creation Alert

This alert was triggered based on the creation of a new domain account with elevated privileges.

🔗 **Alert Logic:** [View Alert Configuration](../alerts/privileged_account_alert.md)

![Persistence Alert](../screenshots/persistence/persistence_alert.png)

**Key Evidence:**

- **Alert Severity:** High
- **New Account:** `socadmin_clean`
- **Privilege Level:** Domain Admin
- **Behavior Pattern:** Account creation followed by privilege escalation
- **Risk:** Long-term unauthorized access

---

## 🧠 Analysis

This phase confirms that the attacker has established persistence within the environment by creating and elevating a new domain account.

Key indicators include:

- Creation of a new domain account using administrative privileges
- Immediate elevation of the account to Domain Admin status
- Establishment of an alternate access path independent of the original compromised account

This behavior ensures that the attacker can retain access even if the initial compromise is detected and remediated.

---

## 🧭 MITRE ATT&CK Mapping

| Technique ID | Technique Name       | Description                        |
| ------------ | -------------------- | ---------------------------------- |
| T1136        | Create Account       | Creation of a new domain account   |
| T1098        | Account Manipulation | Adding account to privileged group |

---

## 🏁 Conclusion

The persistence phase demonstrates that the attacker has secured long-term access to the environment through the creation and elevation of a privileged account.

This represents a critical security risk, as it allows continued unauthorized access even after initial indicators of compromise are addressed.
