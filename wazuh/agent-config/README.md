# 🛡️ Wazuh Agent Configuration

## 📌 1. Overview

The Wazuh Agent runs on the Ubuntu victim machine in the Hydra SOC lab. It collects endpoint security events and forwards them to the Wazuh Manager for centralized analysis and alert generation.

This guide documents the agent installation, Linux Audit integration, and File Integrity Monitoring (FIM) configuration used in the project.

## ⚙️ 2. Install the Wazuh Agent

The agent was installed using the deployment workflow provided by the Wazuh Dashboard.

### 📦 Step 1: Download the agent package

The ARM64 DEB package for Wazuh Agent version `4.14.6-1` was downloaded from the official Wazuh package repository.

```bash
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.14.6-1_arm64.deb
```

### 🔗 Step 2: Install the agent and configure the manager

Replace `<WAZUH_MANAGER_ADDRESS>` with the appropriate manager address in your own environment.

```bash
sudo WAZUH_MANAGER='<WAZUH_MANAGER_ADDRESS>' dpkg -i ./wazuh-agent_4.14.6-1_arm64.deb
```

### 🚀 Step 3: Enable and start the agent

Reload the systemd service configuration:

```bash
sudo systemctl daemon-reload
```

Enable the agent to start automatically at boot:

```bash
sudo systemctl enable wazuh-agent
```

Start the agent:

```bash
sudo systemctl start wazuh-agent
```

### ✅ Step 4: Verify the agent connection

Open **Endpoints** in the Wazuh Dashboard and verify that the agent appears as connected.

## 📋 3. Collect Linux Audit Events

The Wazuh Agent was configured to collect events from the Linux Audit log at `/var/log/audit/audit.log`.

The following entry was added to `/var/ossec/etc/ossec.conf`:

```xml
<localfile>
    <log_format>syslog</log_format>
    <location>/var/log/audit/audit.log</location>
</localfile>
```

This allows the agent to collect Linux Audit events and forward them to the Wazuh Manager.

## 🔍 4. Configure Command Execution Auditing

Linux Audit was used to monitor process-execution events on the Ubuntu victim.

The following runtime audit rule was used:

```bash
sudo auditctl -a always,exit -F arch=b64 -S execve -F key=audit-wazuh-c
```

The rule monitors 64-bit process-execution events and assigns them the audit key `audit-wazuh-c`.

Commands exercised during testing included:

* `whoami`
* `id`
* `uname -a`
* `hostname`
* `ip addr`

The collected events provide input for the custom Wazuh detection rule documented in [`../rules/suspicious-command.xml`](../rules/suspicious-command.xml).

> ⚠️ **Note:** Rules added through `auditctl` are runtime rules and may not persist after a reboot unless persistence is configured separately.

## 🗂️ 5. Configure File Integrity Monitoring (FIM)

File Integrity Monitoring was configured to monitor a dedicated test directory on the Ubuntu victim.

Add the following entry inside the existing `<syscheck>` section of the agent configuration, replacing `<TEST_USERNAME>` with the relevant account name:

```xml
<directories realtime="yes">/home/<TEST_USERNAME>/hydra-fim</directories>
```

The `realtime="yes"` setting enables real-time monitoring of the specified directory.

🧪 **Testing:** Create, modify, and delete test files in this directory to generate file-system activity for testing.

## 📊 6. Configuration Summary

| Component                     | Purpose                                           |
| ----------------------------- | ------------------------------------------------- |
| 🛡️ Wazuh Agent               | Collects and forwards endpoint events             |
| 📝 Linux Audit                | Records selected command-execution events         |
| 📥 Audit log collection       | Makes Linux Audit events available to Wazuh       |
| 🗂️ File Integrity Monitoring | Monitors changes in the designated test directory |
| 🔎 Wazuh Manager              | Analyzes incoming events and generates alerts     |

Together, these components support the endpoint-monitoring and detection scenarios documented in the Hydra SOC repository.
