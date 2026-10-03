# SOC Network Threat Detection Lab

A hands-on Security Operations Center (SOC) lab built using VirtualBox, Kali Linux, Ubuntu, Windows, Splunk Enterprise, Splunk Universal Forwarder, Suricata, and Zeek.

The project demonstrates how security events can be generated from an attacker machine, collected by IDS and system logs, forwarded to Splunk, detected using SPL queries, and visualized through a SOC monitoring dashboard.

## 🏗️ Lab Architecture

| Machine | Role | IP Address |
|---|---|---|
| Kali Linux | Attacker / Security Testing | 192.168.20.11 |
| Ubuntu | IDS / Log Collection / Server | 192.168.20.12 |
| Windows | Splunk Enterprise | 192.168.20.10 |

### Network

```text
                 SOC Lab Network
                 192.168.20.0/24

        ┌──────────────────────┐
        │     Kali Linux       │
        │   Attacker / Tester  │
        │   192.168.20.11      │
        └──────────┬───────────┘
                   │
                   │ Security Testing
                   ▼
        ┌──────────────────────┐
        │       Ubuntu         │
        │ Suricata + Zeek + UF │
        │   192.168.20.12      │
        └──────────┬───────────┘
                   │
                   │ Logs → 9997
                   ▼
        ┌──────────────────────┐
        │       Windows       │
        │  Splunk Enterprise  │
        │   192.168.20.10     │
        └──────────────────────┘
