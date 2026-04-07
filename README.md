# Hybrid-Cloud SIEM Architecture for Decentralized Payment Nodes

## 📋 Objective
The objective of this project was to architect, attack, and defend a hybrid-cloud infrastructure designed to host decentralized agent-to-agent payment ledgers. 

This lab simulates a real-world scenario where a local Linux payment node is targeted by external threat actors. The defense strategy transitions from custom local shell scripting to an enterprise-grade cloud SIEM deployment, demonstrating the scalable security operations required for cross-border financial data compliance.

### Skills Learned
- Architecting hybrid-cloud bridges using **Azure Arc** to connect on-premises Linux infrastructure to Microsoft Azure.
- Engineering **Data Collection Rules (DCR)** to surgically ingest critical authentication telemetry while minimizing cloud storage costs.
- Utilizing **KQL (Kusto Query Language)** to hunt for active threats within a **Microsoft Sentinel** Log Analytics workspace.
- Understanding Linux daemon dependencies (`rsyslog`) for cloud telemetry forwarding.
- Simulating network reconnaissance and brute-force credential stuffing attacks.

### Tools Used
- **Cloud SIEM:** Microsoft Sentinel, Azure Log Analytics, Azure Arc, Azure Monitor Agent (AMA).
- **Target Infrastructure:** Ubuntu Linux Server (Minimal Install).
- **Attacker Infrastructure:** Kali Linux.
- **Security Tools:** Hydra, Nmap, Custom Bash Scripting, `journalctl`.

---

## 🏗️ Architecture & Network Flow

[Insert your Network Topology Diagram here if you create one]

1.  **The Target:** An on-premises Ubuntu server hosting a simulated agent-to-agent `Secure_Vault` ledger.
2.  **The Attack:** A Kali Linux machine on the same subnet attempting to brute-force the server's SSH port.
3.  **The Defense Bridge:** The Ubuntu server is onboarded to Azure Arc. A local `rsyslog` daemon forwards `auth` and `authpriv` logs via the Azure Monitor Agent.
4.  **The SIEM:** Microsoft Sentinel ingests the logs, allowing for KQL-based threat hunting and real-time dashboard alerts.

---

## 👣 Step-by-Step Execution & Artifacts

### Phase 1: Attack Simulation (Red Team)
To generate actionable security telemetry, the perimeter was breached using a dictionary attack.
- Mapped the network to identify the open SSH port on the payment gateway.
- Engineered a targeted dictionary file (`passwords.txt`).
- Executed an intensive credential stuffing attack using **Hydra** until the gateway was compromised.

>

### Phase 2: Local Intrusion Detection 
Before scaling to the cloud, local detection logic was engineered to monitor the vault without relying on heavy third-party software.
- Authored a custom Bash shell script utilizing `grep` to parse live `journalctl` SSH logs.
- Engineered real-time terminal alerts to trigger the moment a "Failed password" string was detected on the local node.

> [Insert Screenshot 2: Your Ubuntu terminal showing the custom Bash alarm triggering]

### Phase 3: Enterprise Cloud Escalation (Blue Team)
To meet the rigorous compliance standards of international financial security operations, the local detection was escalated to Microsoft Azure.
- Successfully bypassed missing OS dependencies by installing and configuring `rsyslog`.
- Onboarded the headless Ubuntu node to **Azure Arc** via Device Code Authentication.
- Configured a Data Collection Rule inside **Microsoft Sentinel** to ingest only relevant authentication logs (`LOG_INFO` level for `auth` and `authpriv`), ensuring cost-effective cloud scaling.

### Phase 4: Threat Hunting with KQL
With the hybrid-cloud pipeline established, the brute-force attack was launched a final time. The resulting telemetry was hunted and identified within the Azure database.

- Authored the following KQL query to isolate the attack vectors:
  ```kusto
  Syslog
  | where Facility in ("auth", "authpriv")
  | where SyslogMessage contains "Failed password"
  | project TimeGenerated, Computer, ProcessName, SyslogMessage
  | sort by TimeGenerated desc
