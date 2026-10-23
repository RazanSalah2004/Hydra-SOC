# 🛡️ Wazuh Detection Rules

This directory contains the detection rules and supporting documentation used in the Hydra SOC project.

## 📋 Rule Inventory

| Security Scenario                    | Detection Mechanism                                | File                                             |
| ------------------------------------ | -------------------------------------------------- | ------------------------------------------------ |
| SSH Brute-Force Detection            | Built-in Wazuh rules `5760` and `5763`             | [ssh-brute-force.md](ssh-brute-force.md)         |
| Suspicious System-Discovery Commands | Custom Wazuh rule `100200`                         | [suspicious-command.xml](suspicious-command.xml) |
| File Integrity Monitoring (FIM)      | Wazuh Syscheck with real-time directory monitoring | Configured on the Ubuntu endpoint                |

## 🔐 SSH Brute-Force Detection

Wazuh's built-in rules identify SSH authentication failures and correlate repeated events to detect potential brute-force activity.

📄 [View SSH Brute-Force Detection and Response](ssh-brute-force.md)

## 🖥️ Suspicious Command Detection

The custom XML rule is designed to detect selected system-discovery commands in Linux Audit (`auditd`) execution events.

* **Rule ID:** `100200`
* **Severity level:** `8`
* **Parent rule:** `80700`
* **Event type:** `EXECVE`

📄 [View the custom detection rule](suspicious-command.xml)

## 📁 File Integrity Monitoring (FIM)

We configured Wazuh Syscheck on the Ubuntu victim to monitor a directory for file changes in real time.

**1. Configure the Wazuh Agent**

Edit `/var/ossec/etc/ossec.conf` on the Ubuntu victim and add this entry inside the existing `<syscheck>` section:

```xml
<directories realtime="yes">/home/<TEST_USERNAME>/hydra-fim</directories>
```

Replace `<TEST_USERNAME>` with the Ubuntu account username.

**2. Validate and restart the agent**

```bash
sudo /var/ossec/bin/wazuh-agentd -t
sudo systemctl restart wazuh-agent
sudo systemctl status wazuh-agent
```

**3. Test file changes**

```bash
touch /home/<TEST_USERNAME>/hydra-fim/test-file.txt
echo "Test modification" >> /home/<TEST_USERNAME>/hydra-fim/test-file.txt
rm /home/<TEST_USERNAME>/hydra-fim/test-file.txt
```

These commands test file creation, modification, and deletion. Confirm the corresponding alerts in the Wazuh Dashboard before claiming that each operation was detected.

---

**Hydra SOC — Collect. Detect. Investigate. Respond.**
