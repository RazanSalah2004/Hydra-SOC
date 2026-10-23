# 🔐 SSH Brute-Force Detection and Active Response

## 📌 Overview

SSH brute-force detection was tested using Hydra against the Ubuntu victim machine. Wazuh collected authentication logs, detected repeated login failures, and triggered an Active Response that blocked the attacker's source IP address.

## 📥 Log Collection

* **📄 Log file:** `/var/log/auth.log`
* **📋 Log format:** Syslog
* **🖥️ Agent:** Wazuh Agent

The following configuration was added to the Wazuh agent to collect SSH authentication events:

```xml
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/auth.log</location>
</localfile>
```

## 🔎 Detection Rules

| Rule ID | Description                | Level |
| ------- | -------------------------- | ----: |
| `5760`  | SSH authentication failure |     5 |
| `5763`  | SSH brute-force detection  |    10 |

Rule `5760` detects SSH authentication failures. Rule `5763` correlates repeated failures to identify potential brute-force activity.

## 🔗 Correlation Logic

Rule `5763` uses the following conditions:

| Setting            | Value               |
| ------------------ | ------------------- |
| Frequency          | 8 events            |
| Time window        | 120 seconds         |
| Source IP matching | Same source IP      |
| Ignore period      | 60 seconds          |
| MITRE ATT&CK       | T1110 — Brute Force |

The rule triggers when the configured threshold of repeated authentication failures is reached within the time window. The ignore period limits how frequently the rule generates alerts.

## 🛡️ Active Response

The Wazuh manager was configured to execute `firewall-drop` when rule `5763` triggers.

```xml
<command>
  <name>firewall-drop</name>
  <executable>firewall-drop</executable>
  <timeout_allowed>yes</timeout_allowed>
</command>

<active-response>
  <command>firewall-drop</command>
  <location>local</location>
  <rules_id>5763</rules_id>
  <timeout>180</timeout>
</active-response>
```

* **⚙️ Response command:** `firewall-drop`
* **📍 Execution location:** `local`
* **🎯 Trigger rule:** `5763`
* **⏱️ Block timeout:** 180 seconds

## 🧪 Test Result

Hydra was used to generate repeated SSH login failures against the Ubuntu victim. Wazuh detected the activity, and the team confirmed that Active Response blocked the attacker's source IP address.

## 🧰 Tools

* ⚡ Hydra
* 🛡️ Wazuh Manager
* 📡 Wazuh Agent
* 🐧 Ubuntu
