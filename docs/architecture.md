# 🛡️ Hydra SOC Architecture

## 📌 1. Project Overview

**Hydra SOC is a hands-on Security Operations Center (SOC) implementation built with Wazuh, focusing on security monitoring, detection engineering, event correlation, and incident response in a controlled environment.**

The project integrates attack simulation, endpoint log collection, custom detection rules, and security alert investigation into a practical monitoring workflow.

The environment consists of an Ubuntu victim machine running the Wazuh Agent and a dedicated Wazuh server hosting the SOC platform. It demonstrates how suspicious activities generate security events, how those events are analyzed by Wazuh, and how detection rules can identify potentially malicious behavior.

🎯 **The project focuses on four security use cases:**

* 🔐 SSH brute-force detection
* 💻 Suspicious command execution
* 📂 File Integrity Monitoring (FIM)
* 🚨 Event correlation and incident response

## 🏗️ 2. System Architecture

The project is organized into three operational roles.

### 🔴 2.1 Red Team — Attack Simulation

The Red Team simulates selected attack techniques against the designated Ubuntu victim machine in the controlled lab environment.

The objective is to generate security events that can be collected, analyzed, and investigated by the Blue Team.

**Tool used for SSH attack simulation:**

* ⚡ **Hydra:** Performs repeated SSH authentication attempts to test the detection of suspicious authentication activity.

The attack simulations are conducted within the authorized project environment.

### 🖥️ 2.2 Ubuntu Victim Machine

The victim machine runs Ubuntu and the Wazuh Agent.

**Its responsibilities include:**

* 📝 Providing relevant authentication and system logs.
* 🔍 Recording selected command executions through Linux Audit (`auditd`).
* 📂 Monitoring selected files and directories through Wazuh File Integrity Monitoring.
* 📡 Forwarding configured security events to the Wazuh Manager for analysis.

The victim machine serves as the monitored endpoint in the SOC architecture.

### 🔵 2.3 Blue Team — Wazuh SOC Server

The Blue Team operates the Wazuh platform to monitor, detect, and investigate suspicious activity.

The Wazuh server hosts the following components:

#### ⚙️ Wazuh Manager

* Receives events from connected agents.
* Decodes incoming events.
* Evaluates events against configured detection rules.
* Generates alerts when rule conditions are met.
* Supports configured Active Response mechanisms.

#### 🗄️ Wazuh Indexer

* Stores and indexes security data.
* Supports searching and analysis of indexed events and alerts.

#### 📊 Wazuh Dashboard

* Provides a graphical interface for monitoring security alerts.
* Enables analysts to inspect event details and investigate suspicious activity.

Together, these components support centralized security monitoring and investigation.

## 🔄 3. Security Event Processing Workflow

The project's monitoring workflow follows these stages:

1. 🎯 **Attack Simulation:** The Red Team performs a selected test against the Ubuntu victim machine.
2. 📝 **Event Generation:** The activity generates relevant authentication, system, audit, or file-integrity events.
3. 📡 **Log Collection:** The Wazuh Agent collects configured logs and security events.
4. 🔗 **Event Forwarding:** The agent forwards the collected events to the Wazuh Manager.
5. 🧠 **Event Analysis:** The manager decodes events and evaluates them against applicable detection rules.
6. 🚨 **Alert Generation:** When an event matches the conditions of a detection rule, Wazuh generates an alert.
7. 🔍 **Alert Investigation:** The Blue Team reviews the resulting alerts through the Wazuh Dashboard.
8. 🛡️ **Incident Response:** Where configured and successfully tested, Active Response can execute an automated action for a qualifying event.

This workflow demonstrates the relationship between attack activity, endpoint telemetry, detection logic, alert investigation, and response.

## 🔍 4. Detection and Monitoring Use Cases

### 🔐 4.1 SSH Brute-Force Detection

This scenario investigates repeated SSH authentication failures generated through an attack simulation using Hydra.

The objective is to identify suspicious authentication activity through Wazuh rules and explore event correlation based on repeated failures.

The project also explores configuring an automated response to qualifying SSH brute-force activity.

### 💻 4.2 Suspicious Command Execution

This scenario investigates selected system-discovery commands executed on the Ubuntu victim machine.

Linux Audit (`auditd`) records configured command-execution events, which can be collected and analyzed by Wazuh. Custom detection rules are used to identify selected commands for further investigation.

⚠️ These commands are not inherently malicious; their significance depends on the surrounding activity and context.

### 📂 4.3 File Integrity Monitoring (FIM)

This scenario uses Wazuh File Integrity Monitoring to monitor selected files and directories.

The objective is to identify file changes that may indicate unauthorized modification or other suspicious activity.

### 🚨 4.4 Event Correlation and Incident Response

This use case explores how repeated or related security events can be evaluated together to identify potentially significant activity.

The project also investigates Active Response for selected events where the response mechanism has been configured and tested.

> 📌 Detection and response results are documented according to the tests actually completed.

## 🛠️ 5. Technologies and Tools

| Technology                | Purpose                                                                 |
| ------------------------- | ----------------------------------------------------------------------- |
| 🛡️ Wazuh                 | Security monitoring, event analysis, detection, and alert investigation |
| 🖥️ Ubuntu                | Victim machine and SOC server operating environment                     |
| 📡 Wazuh Agent            | Collection and forwarding of configured endpoint events                 |
| 🔍 Linux Audit (`auditd`) | Recording selected system audit events                                  |
| 🖥️ VMware                | Virtual machine environment                                             |
| 🔗 Tailscale              | Connectivity between participating lab machines                         |
| ⚡ Hydra                   | SSH authentication testing                                              |

## 🌐 6. Lab Environment and Connectivity

The project uses VMware to host the SOC server and establish the required virtualized environment.

Tailscale provides connectivity between participating machines where configured.

The Ubuntu victim machine communicates with the Wazuh Manager through the configured network connection, allowing collected security events to be analyzed centrally.

🔒 Specific IP addresses, credentials, authentication secrets, and other sensitive configuration details are intentionally excluded from this public documentation.

## ⚠️ 7. Security Scope and Limitations

Hydra SOC is an educational project designed for authorized security testing in a controlled lab environment.

The project demonstrates selected SOC workflows rather than claiming complete enterprise-grade detection coverage.

Detection effectiveness depends on the available telemetry, agent configuration, detection rules, and testing conditions. Automated response also requires careful configuration and validation to reduce the risk of disrupting legitimate activity.

## 🎯 8. Conclusion

Hydra SOC demonstrates a practical approach to building and evaluating a Wazuh-based security monitoring environment.

By integrating attack simulation, endpoint log collection, custom detection rules, alert investigation, and selected response mechanisms, the project explores how a SOC can transform raw security events into actionable information.

The project also highlights the importance of accurate detection logic, event correlation, false-positive investigation, and verification of response behavior in building reliable security monitoring capabilities.

---

### 🛡️ Hydra SOC

**Collect. Detect. Investigate. Respond.**
