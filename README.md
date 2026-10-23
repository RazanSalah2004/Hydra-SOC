# Hydra SOC 🛡️

### A Practical Security Operations Center Using Wazuh

## 📌 Overview

**Hydra SOC** is a hands-on Security Operations Center (SOC) project designed to demonstrate how security events can be collected, monitored, analyzed, and detected in a controlled lab environment.

The project uses **Wazuh** as its primary security monitoring and detection platform. It brings together attack simulation, endpoint monitoring, custom detection rules, event correlation, and incident response to demonstrate key SOC workflows.

## 🎯 Project Objectives

* Build a functional SOC lab environment.
* Configure centralized security monitoring using Wazuh.
* Collect and analyze endpoint security logs.
* Develop and test custom detection rules.
* Explore event correlation for identifying suspicious activity.
* Investigate security alerts and potential false positives.
* Explore automated response to selected security events.

## 🏗️ Architecture

The lab is organized around three main roles:

| Role                 | Responsibility                                                                       |
| -------------------- | ------------------------------------------------------------------------------------ |
| 🔴 Red Team          | Simulates selected attacks in the controlled lab environment.                        |
| 🟡 Victim / Endpoint | Runs Ubuntu Linux and the Wazuh Agent to provide security events and logs.           |
| 🔵 Blue Team / SOC   | Operates the Wazuh Manager and monitors security alerts through the Wazuh Dashboard. |

### Wazuh Components

* **Wazuh Agent:** Collects configured security events and logs from the monitored endpoint.
* **Wazuh Manager:** Analyzes incoming events and applies detection rules.
* **Wazuh Indexer:** Stores and indexes security data for searching and analysis.
* **Wazuh Dashboard:** Provides a visual interface for monitoring and investigating security alerts.

## 🔍 Attack Scenarios

The project explores the following security use cases:

1. **SSH Brute-Force Detection** — Monitoring repeated SSH authentication failures.
2. **Suspicious Command Execution** — Using Linux Audit (`auditd`) and Wazuh to investigate selected system-discovery commands.
3. **File Integrity Monitoring (FIM)** — Monitoring selected files and detecting changes.
4. **Event Correlation and Incident Response** — Exploring how related events can trigger higher-priority alerts and, where configured and verified, automated responses.

> Detection and response results are documented according to the tests actually completed in the lab.

## 🛠️ Technologies

* Wazuh
* Ubuntu Server
* Ubuntu Linux
* Linux Audit (`auditd`)
* VMware
* Tailscale
* Nmap
* Hydra
* FFUF

## 📂 Repository Structure

```text
Hydra-SOC/
├── README.md
├── docs/
│   ├── architecture.md
│   ├── attack-scenarios.md
│   └── screenshots/
├── wazuh/
│   ├── rules/
│   ├── agent-config/
│   └── active-response/
├── attacks/
│   ├── ssh-brute-force.md
│   ├── suspicious-command-execution.md
│   └── file-integrity-monitoring.md
└── .gitignore
```

*The structure above describes the planned documentation and configuration layout; files will be added as they are prepared.*

## 🔐 Scope and Safety

All attack simulations are intended for an authorized, controlled lab environment.

This repository is intended for educational and defensive security purposes. Do not use the documented techniques against systems without explicit authorization.

## 👥 Team

Hydra SOC is a collaborative academic project involving Red Team simulation, endpoint monitoring, and Blue Team detection and investigation.

## 🚧 Project Status

The project demonstrates selected SOC workflows in a lab environment. Detection coverage, correlation behavior, and automated response are evaluated through individual tests; not every scenario should be assumed to be fully automated or successfully detected.

---

**Hydra SOC — Collect. Detect. Investigate. Respond.**
