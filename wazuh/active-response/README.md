# 🚨 Wazuh Active Response

## 📌 Overview

Wazuh Active Response enables automated defensive actions when security events meet defined detection criteria.

In Hydra SOC, Active Response is configured to use `firewall-drop` for detected SSH brute-force activity.

## 🔄 Response Workflow

1. 🔍 The Wazuh Agent forwards SSH authentication events to the Wazuh Manager.
2. 🚨 Wazuh analyzes the events using its detection rules.
3. ⚠️ Rule `5763` identifies a correlated SSH brute-force pattern.
4. 🛡️ The configured Active Response invokes `firewall-drop`.
5. ⏱️ The response timeout is configured for 180 seconds.

## ⚙️ Configuration

| Setting            | Value           |
| ------------------ | --------------- |
| Response mechanism | `firewall-drop` |
| Execution location | Wazuh Manager   |
| Triggering rule    | `5763`          |
| Response timeout   | 180 seconds     |

## 🛡️ Firewall Response

The `firewall-drop` mechanism is designed to block traffic from a specified source address using firewall rules. In this scenario, it is intended to help mitigate repeated SSH connection attempts associated with detected brute-force activity.

## 🔗 Related Detection Rules

SSH detection details are documented in [`../rules/ssh-brute-force.md`](../rules/ssh-brute-force.md).

* **Rule 5760:** SSH authentication failure.
* **Rule 5763:** Correlated SSH brute-force detection.

## 🧪 Validation

Validation should confirm:

* Detection of the SSH brute-force pattern.
* Invocation of the Active Response.
* Application of the firewall block.
* Expiration of the block after the configured timeout.

A detection alert alone does not confirm that the firewall response succeeded.

## 🔐 Security Considerations

* Avoid blocking legitimate traffic.
* Review response logs when investigating unexpected behavior.
* Verify firewall behavior in the deployment environment.
* Do not expose sensitive network details in public documentation.
