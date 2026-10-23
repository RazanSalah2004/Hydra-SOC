# 🛡️ Hydra SOC — Attack Scenarios and Detection Validation

## 📌 Overview

Hydra SOC is a hands-on Security Operations Center (SOC) project built around Wazuh for security monitoring, threat detection, event correlation, and incident response.

The project implements four main security scenarios:

1. 🔐 SSH brute-force detection and response.
2. 🖥️ Suspicious system-discovery command detection.
3. 📁 File Integrity Monitoring (FIM).
4. 🚨 Event correlation and incident response.

---

## 🔐 Scenario 1: SSH Brute-Force Detection

### 🎯 Objective

Detect repeated SSH authentication failures and evaluate Wazuh's ability to identify brute-force activity and trigger an automated response.

### 🧪 Attack Simulation

Hydra was used to generate repeated SSH authentication attempts against the Ubuntu victim machine in the lab environment.

### 📥 Log Collection

* **Log source:** `/var/log/auth.log`
* **Log format:** Syslog
* **Collection mechanism:** Wazuh Agent

The Wazuh agent was configured to monitor SSH authentication logs and forward relevant events to the Wazuh manager for analysis.

### 🔎 Detection and Correlation

Wazuh analyzes SSH authentication events to identify failed login attempts and repeated authentication failures.

* **Rule ID `5760`:** SSH authentication failure detection.
* **Rule ID `5763`:** SSH brute-force detection and correlation in the tested environment.

Correlation of repeated authentication failures helps identify suspicious login activity that may not be apparent from a single failed attempt.

### 🛡️ Automated Response

Wazuh Active Response was configured and tested as part of the SSH brute-force scenario. The team confirmed that the attacker's source IP address was blocked.

This demonstrated the integration of security detection and automated response within the lab environment.

---

## 🖥️ Scenario 2: Suspicious System-Discovery Command Detection

### 🎯 Objective

Detect selected Linux commands that may be used to gather system information during suspicious activity.

### 🧪 Test Commands

The following commands were executed during testing:

* `whoami`
* `hostname`
* `id`
* `uname -a`
* `ip address`

These commands are legitimate system utilities. Their execution alone does not establish malicious intent, but monitoring their execution can provide useful context when investigating suspicious activity.

### 📥 Log Collection

Linux Audit (`auditd`) was configured to record command-execution events. The Wazuh agent collects the relevant audit logs and forwards them to the Wazuh manager.

* **Log source:** `/var/log/audit/audit.log`
* **Audit event key:** `audit-wazuh-c`
* **Collection mechanism:** `auditd` and Wazuh Agent

### 🔎 Detection Rule

A custom Wazuh rule was implemented to detect selected system-discovery commands in audit events.

| Attribute      | Value                                        |
| -------------- | -------------------------------------------- |
| Rule ID        | `100200`                                     |
| Description    | Suspicious system discovery command executed |
| Severity level | 8                                            |
| Log source     | Linux Audit                                  |

### 🛠️ Rule Validation and Tuning

The initial implementation generated false positives from unrelated audit events. The detection logic was reviewed and adjusted to address the issue.

Subsequent testing confirmed that the false-positive issue was resolved, improving the relevance of the generated alerts.

---

## 📁 Scenario 3: File Integrity Monitoring (FIM)

### 🎯 Objective

Monitor selected files on the Ubuntu victim machine and detect changes that may indicate unauthorized or unexpected modifications.

### 🔎 Monitoring Mechanism

Wazuh File Integrity Monitoring monitors configured files and directories for changes to their integrity.

FIM events provide visibility into file modifications and support investigations into potentially suspicious changes.

### 📥 Event Collection

The Wazuh agent collects FIM events from the monitored Ubuntu system and forwards them to the Wazuh manager for analysis and presentation in the Wazuh dashboard.

---

## 🚨 Scenario 4: Event Correlation and Incident Response

### 🎯 Objective

Correlate related security events to identify suspicious activity and connect detection with an appropriate response.

### 🔗 Event Correlation

The SSH brute-force scenario demonstrates how repeated authentication failures can be evaluated together rather than treated as isolated events.

Wazuh correlation rules use configured conditions, such as event frequency and time windows, to identify repeated activity that meets the detection criteria.

### 🛡️ Incident Response

Wazuh Active Response was tested in conjunction with SSH brute-force detection.

After the detection and response process was triggered, the team confirmed that the attacker's source IP address was blocked.

This demonstrated the connection between event monitoring, correlation, alert generation, and automated response in the Hydra SOC environment.

---
## 🔒 Security Considerations

* 🧪 All attack simulations were performed in the project's lab environment.
* 🔐 Sensitive information, including credentials and private IP addresses, is excluded from published configurations and evidence.
* 🛠️ Custom detection rules were tested and tuned to reduce false-positive alerts.



---

## 🏁 Summary

Hydra SOC demonstrates a practical approach to security monitoring and incident response using Wazuh, Ubuntu, and Linux audit telemetry.

The project covers SSH brute-force detection and automated response, suspicious system-discovery command detection, File Integrity Monitoring, and event correlation.

These scenarios illustrate how endpoint telemetry, detection rules, correlation logic, and automated response can work together to support security monitoring and investigation.

### 🛡️ Collect. Detect. Investigate. Respond.
