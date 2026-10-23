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

```bash
hydra -l <TEST_USERNAME> -P <PASSWORD_LIST> ssh://<VICTIM_IP> -t 2
```

**Command parameters:**

* `-l`: Specifies the username being tested.
* `-P`: Specifies the password list.
* `ssh://`: Identifies SSH as the target service.
* `-t 2`: Uses two parallel task threads.

### 📥 Log Collection

* **Log source:** `/var/log/auth.log`
* **Log format:** Syslog
* **Collection mechanism:** Wazuh Agent

The Wazuh agent was configured to monitor SSH authentication logs and forward relevant events to the Wazuh manager for analysis.

### 🔎 Detection and Correlation

Wazuh analyzes SSH authentication events to identify failed login attempts and repeated authentication failures.

* **Rule ID `5760`:** SSH authentication failure detection.
* **Rule ID `5763`:** SSH brute-force detection and correlation in the tested environment.

Rule `5763` uses the following correlation conditions:

| Setting            | Value               |
| ------------------ | ------------------- |
| Frequency          | 8 events            |
| Time window        | 120 seconds         |
| Source IP matching | Same source IP      |
| Ignore period      | 60 seconds          |
| MITRE ATT&CK       | T1110 — Brute Force |

Correlation of repeated authentication failures helps identify suspicious login activity that may not be apparent from a single failed attempt.

### 🛡️ Automated Response

Wazuh Active Response was configured to execute `firewall-drop` when rule `5763` triggers.

* **Response command:** `firewall-drop`
* **Execution location:** `local`
* **Block timeout:** 180 seconds

Testing confirmed that Active Response blocked the attacker's source IP address.

The detailed log collection, detection, correlation, and Active Response configuration is documented in [`wazuh/rules/ssh-brute-force.md`](../wazuh/rules/ssh-brute-force.md).

---

## 🖥️ Scenario 2: Suspicious System-Discovery Command Detection

### 🎯 Objective

Detect selected Linux commands that may be used to gather system information during suspicious activity.

### 🧪 Test Commands

An SSH connection was established to the Ubuntu victim machine:

```bash
ssh <TEST_USERNAME>@<VICTIM_IP>
```

The following commands were then executed within the SSH session:

```bash
whoami
id
uname -a
hostname
ip addr
```

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

### 🧪 Test Operations

The following operations were performed on a test file inside the monitored directory:

`/home/<TEST_USERNAME>/<MONITORED_DIRECTORY>/`

**Create a file:**

```bash
touch /home/<TEST_USERNAME>/<MONITORED_DIRECTORY>/test-file.txt
```

**Modify the file:**

```bash
echo "Test modification" >> /home/<TEST_USERNAME>/<MONITORED_DIRECTORY>/test-file.txt
```

**Delete the file:**

```bash
rm /home/<TEST_USERNAME>/<MONITORED_DIRECTORY>/test-file.txt
```

These operations test file creation, modification, and deletion within the monitored directory.

If a permission error occurs, `sudo` may be used where necessary, provided the account is authorized to perform the operation.

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

Wazuh rule `5763` correlates repeated SSH authentication failures using the configured event frequency, time window, and source IP conditions.

When the rule triggers, the associated Active Response executes `firewall-drop` according to the configured response settings.

### 🛡️ Incident Response

During SSH brute-force testing, the team confirmed that Active Response blocked the attacker's source IP address.

This demonstrated the connection between endpoint monitoring, event correlation, alert generation, and automated response in the Hydra SOC environment.

---

## 🔒 Security Considerations

* 🧪 All attack simulations were performed in the project's lab environment.
* 🔐 Credentials, access tokens, private keys, and unnecessary infrastructure details are excluded from published configurations and evidence.
* 🛠️ Custom detection rules were tested and tuned to reduce false-positive alerts.
* 🎯 System-discovery commands and file operations were performed as controlled tests in the lab.

---

## 🏁 Summary

Hydra SOC uses Wazuh, Ubuntu, and Linux audit telemetry to demonstrate threat detection, event correlation, File Integrity Monitoring, and automated incident response.

### 🛡️ Collect. Detect. Investigate. Respond.


### 🛡️ Collect. Detect. Investigate. Respond.
