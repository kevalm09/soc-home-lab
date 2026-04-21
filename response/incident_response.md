# 🛡️ Incident Response & Remediation

---

## 📌 Overview

This section outlines the response actions taken following the detection of malicious activity across the environment. Based on the investigation findings, multiple stages of the attack lifecycle were identified, including reconnaissance, internal discovery, credential abuse, lateral movement, and persistence.

The response focuses on **containment, eradication, and recovery**, with the goal of removing attacker access and preventing future compromise.

---

## 🚨 Incident Summary

The investigation confirmed that an attacker:

- Performed reconnaissance to identify domain infrastructure and privileged accounts
- Conducted internal service enumeration to identify accessible services
- Attempted credential abuse through repeated failed authentication attempts
- Successfully authenticated to the domain controller via SMB
- Established persistence by creating and elevating a new domain account

This represents a **full domain compromise scenario**.

---

## 🛑 Containment Actions

Immediate steps were taken to limit attacker access and prevent further spread.

**Actions:**

- Disabled compromised account: `socadmin`
- Disabled unauthorized account: `socadmin_clean`
- Terminated active SMB sessions to the domain controller
- Isolated affected workstation (`10.0.2.4`) from the network

---

## 🧹 Eradication Actions

Steps were taken to remove attacker artifacts and eliminate persistence mechanisms.

**Actions:**

- Removed unauthorized accounts from the domain
- Reviewed and reverted changes to privileged groups (Domain Admins)
- Cleared cached credentials and active sessions
- Conducted system scans for additional malicious artifacts

---

## 🔄 Recovery Actions

Systems were restored to a secure operational state.

**Actions:**

- Reset passwords for all privileged accounts
- Re-enabled legitimate accounts after verification
- Restored affected systems to a known-good state
- Monitored systems for signs of re-compromise

---

## 🔐 Security Improvements

To prevent future incidents, the following measures are recommended:

- **Enable account lockout policies** to prevent brute-force attacks
- **Implement multi-factor authentication (MFA)** for privileged accounts
- **Restrict SMB access** to necessary systems only
- **Enhance logging and monitoring** across endpoints and network traffic
- **Deploy endpoint detection and response (EDR)** solutions
- **Conduct regular security audits and access reviews**

---

## 🧠 Lessons Learned

This incident highlights several key security gaps:

- Lack of protections against brute-force authentication attempts
- Overexposure of critical services (SMB) within the internal network
- Insufficient monitoring of internal traffic patterns
- Absence of controls preventing unauthorized privilege escalation

Addressing these gaps is critical to strengthening the organization’s security posture.

---

## 🏁 Conclusion

The response actions successfully contained and removed the attacker from the environment, while restoring system integrity.

This investigation demonstrates the importance of:

- Multi-layered detection (endpoint + network)
- Timely alerting and response
- Strong access control and monitoring practices

Implementing the recommended security improvements will significantly reduce the risk of future compromise.
