# 🛡️ Incident Response & Remediation

---

## 📌 Overview

This section documents the response actions performed after detecting and investigating malicious activity within the Azure Active Directory lab environment.

The investigation identified multiple stages of attacker activity, including reconnaissance, credential abuse, internal discovery, privileged access, and persistence.

The response focused on **containment, eradication, and recovery**, with the objective of removing unauthorized access, eliminating persistence mechanisms, and restoring secure administrative access.

---

## 🚨 Incident Summary

The investigation identified the following attacker activity:

- Performed reconnaissance to identify domain infrastructure and privileged accounts
- Conducted internal service discovery against the Domain Controller
- Performed repeated authentication attempts against the privileged `socadmin` account
- Obtained and used compromised `socadmin` credentials to launch a Command Prompt under the domain administrator security context
- Created a new domain account (`attacker1`)
- Added `attacker1` to the **Domain Admins** group to establish persistent privileged access

The combination of compromised privileged credentials and the creation of an additional privileged account resulted in persistent administrative access within the lab environment.

---

## 🛑 Containment

Containment actions focused on preventing continued use of the compromised administrative credentials and establishing a trusted administrative account for remediation.

### Actions Performed

- Created a trusted administrative account (`socadmin_clean`)
- Disabled the compromised `socadmin` account
- Restricted the attacker's ability to continue using the compromised administrative account

These actions prevented continued use of the original compromised account while providing a trusted administrative account for remediation activities.

---

## 🧹 Eradication

Eradication focused on removing the persistence mechanisms established during the attack.

### Actions Performed

- Removed the attacker-created account (`attacker1`)
- Removed the unauthorized privileged group membership
- Reviewed the **Domain Admins** group for unauthorized changes
- Removed the persistence mechanism created during the attack

The account creation and privileged group modification were identified through Windows security events:

- **Event ID 4720** — User Account Created
- **Event ID 4728** — Member Added to Security-Enabled Global Group

---

## 🔄 Recovery

Recovery actions focused on restoring secure administrative access and returning the environment to a known-good state.

### Actions Performed

- Restored secure administrative access using the trusted administrative account
- Re-enabled legitimate administrative access after verification
- Reset credentials for affected privileged accounts
- Reviewed privileged account access
- Returned the environment to a known-good administrative state

Following remediation, the environment was reviewed for remaining unauthorized accounts and privileged group modifications.

---

## 🔐 Security Improvements

Based on the investigation, several security improvements were identified for the environment:

- **Enable account lockout policies** to reduce the effectiveness of repeated authentication attempts
- **Implement multi-factor authentication (MFA)** for privileged accounts
- **Restrict SMB access** to systems that require it
- **Enhance endpoint and network monitoring** for authentication and lateral movement activity
- **Deploy endpoint detection and response (EDR)** for additional endpoint visibility
- **Conduct regular privileged account reviews**
- **Monitor privileged group membership changes**
- **Review and alert on unauthorized account creation**

---

## 🧠 Lessons Learned

This investigation highlighted several areas where additional security controls and monitoring could improve detection and response.

### Credential Protection

Repeated failed authentication attempts demonstrated the importance of protections against brute-force and password-guessing activity.

### Privileged Account Monitoring

The compromise of a privileged account demonstrated the importance of monitoring administrative authentication and privileged account usage.

### Persistence Detection

The creation of `attacker1` followed by its addition to **Domain Admins** demonstrated how attackers can establish an alternate privileged access path.

The combination of Event ID `4720` and Event ID `4728` provided visibility into both stages of this activity.

### Multi-Layer Monitoring

The investigation relied on telemetry from multiple sources, including:

- Windows Security Logs
- pfSense firewall logs
- Zeek network telemetry
- Splunk detections and alerts

Correlating these sources provided additional context throughout the investigation.

---

## 🏁 Conclusion

The response process removed the attacker-created persistence mechanisms and restored secure administrative access within the lab environment.

The investigation demonstrated the importance of combining **endpoint, authentication, firewall, and network telemetry** throughout the incident lifecycle.

The project also demonstrated how detection engineering and alerting can support incident response by providing actionable evidence for:

- Credential abuse
- Internal discovery
- Privileged access
- Account creation
- Privileged group modification

The overall workflow followed:

**Detection → Investigation → Containment → Eradication → Recovery**
