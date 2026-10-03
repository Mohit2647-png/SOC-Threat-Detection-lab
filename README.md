# SOC Network Threat Detection Lab

> A hands-on SOC home lab demonstrating attack simulation, network monitoring, detection engineering, and security event analysis using Splunk, Suricata, Zeek, Kali Linux, Ubuntu, and Windows.

---

## Skills Demonstrated

`SIEM Querying (SPL)` · `Network Traffic Analysis (Zeek)` · `IDS (Suricata)` · `Detection Engineering` · `Attack Simulation` · `Linux Administration` · `Windows Security Monitoring` · `Incident Detection` · `Security Alerting` · `SOC Dashboard Development`

---
# Disclaimer

This project was created strictly for educational and cybersecurity learning purposes inside an isolated VirtualBox lab environment.

All attack simulations were performed against systems controlled by the author.

Do not use these techniques against systems or networks without proper authorization.
## Lab Overview

### Host Machine

| Component | Details |
|---|---|
| OS | Windows |
| Role | VirtualBox Host |
| Platform | Oracle VirtualBox |

### Virtual Machines

| VM | OS | Role | Purpose |
|---|---|---|---|
| Kali Linux | Kali Linux | Attacker | Attack simulation and security testing |
| Ubuntu | Ubuntu Server | IDS / Network Server | Suricata, Zeek, Apache, SSH and Splunk Universal Forwarder |
| Windows | Windows | SIEM / Victim | Splunk Enterprise and Windows Security monitoring |

---

## Network Configuration

### IP Address Assignments

| Machine | Interface | IP Address | Network |
|---|---|---|---|
| Kali Linux | eth0 | `192.168.20.11/24` | SOC Internal Network |
| Ubuntu | enp0s8 | `192.168.20.12/24` | SOC Internal Network |
| Windows | Ethernet | `192.168.20.10/24` | SOC Internal Network |

### Ubuntu Internet/NAT Interface

| Interface | IP Address | Purpose |
|---|---|---|
| enp0s3 | `10.0.2.15/24` | VirtualBox NAT / Internet |
| enp0s8 | `192.168.20.12/24` | SOC Internal Network |

---

## Network Architecture

```text
                    Internet
                       |
                VirtualBox NAT
                       |
                +--------------+
                |    Ubuntu    |
                | 192.168.20.12|
                |              |
                |  Suricata    |
                |  Zeek        |
                |  Apache      |
                |  SSH         |
                |  Splunk UF   |
                +------+-------+
                       |
              SOC Internal Network
                192.168.20.0/24
                 /             \
                /               \
       +---------------+   +---------------+
       |   Kali Linux  |   |    Windows    |
       | 192.168.20.11 |   | 192.168.20.10 |
       |               |   |               |
       | Attack/Test   |   | Splunk        |
       | Nmap          |   | Enterprise    |
       | NetExec       |   | SIEM          |
       +---------------+   +---------------+
```
## Security Monitoring Pipeline
``` text
Kali Linux
    |
    | Attack Simulation
    v
Ubuntu
    |
    +--> Suricata --> eve.json
    |
    +--> Zeek ------> conn.log
    |                 dns.log
    |                 http.log
    |
    +--> auth.log
    |
    v
Splunk Universal Forwarder
    |
    | TCP 9997
    v
Windows Splunk Enterprise
    |
    v
Detection Rules + Alerts + Dashboard
```
## Network configuration
| VM         | Interface | IP Address         | Purpose              |
| ---------- | --------- | ------------------ | -------------------- |
| Kali Linux | `eth0`    | `192.168.20.11/24` | SOC Internal Network |
| Ubuntu     | `enp0s8`  | `192.168.20.12/24` | SOC Internal Network |
| Windows    | Ethernet  | `192.168.20.10/24` | SOC Internal Network |

| Interface | IP Address         | Purpose                   |
| --------- | ------------------ | ------------------------- |
| `enp0s3`  | `10.0.2.15/24`     | Internet / VirtualBox NAT |
| `enp0s8`  | `192.168.20.12/24` | SOC Internal Network      |

## Log Collection

Splunk Universal Forwarder on Ubuntu forwards security telemetry to Splunk Enterprise running on Windows.
## splunk receiver
192.168.20.10:9997
## Monitored Logs
/var/log/suricata/eve.json
/var/log/auth.log
/home/ubun/Desktop/conn.log
/home/ubun/Desktop/http.log
/home/ubun/Desktop/dns.log
## Splunk Indexes
| Index         | Data                       |
| ------------- | -------------------------- |
| `suricata`    | Suricata network events    |
| `zeek`        | Zeek network telemetry     |
| `ubuntu-auth` | Ubuntu authentication logs |
| `windows10`   | Windows Security Events    |
## Detection Engineering

Multiple attacks were simulated from Kali Linux and detected using Suricata, Zeek, Windows Security Logs, Ubuntu authentication logs, and Splunk.
| Detection          | Source          | Technology                     |
| ------------------ | --------------- | ------------------------------ |
| Nmap Port Scanning | Kali → Windows  | Suricata + Splunk              |
| SSH Brute Force    | Kali → Ubuntu   | Ubuntu Auth Logs + Splunk      |
| SMB Brute Force    | Kali → Windows  | Windows Security Logs + Splunk |
| HTTP Beaconing     | Network Traffic | Zeek + Splunk                  |
| Reverse Shell      | Kali ↔ Ubuntu   | Zeek + Splunk                  |
| DNS Anomaly        | DNS Traffic     | Zeek + Splunk                  |
# Tools and Technologies
| Tool                       | Purpose                                           |
| -------------------------- | ------------------------------------------------- |
| Kali Linux                 | Attack simulation and penetration testing         |
| Ubuntu                     | Network server, IDS and log forwarding            |
| Windows                    | Splunk Enterprise and Windows security monitoring |
| Splunk Enterprise          | SIEM, log analysis and detection                  |
| Splunk Universal Forwarder | Log collection and forwarding                     |
| Suricata                   | Network Intrusion Detection System                |
| Zeek                       | Network Security Monitoring                       |
| Apache                     | HTTP traffic generation                           |
| Nmap                       | Network reconnaissance                            |
| NetExec                    | SMB authentication testing                        |
| VirtualBox                 | Virtual SOC infrastructure                        |
Project Objectives

## The objectives of this project were:

Build an isolated SOC environment using VirtualBox
Configure a multi-VM security network
Generate realistic security events
Collect network and authentication telemetry
Deploy Suricata for network intrusion detection
Deploy Zeek for network security monitoring
Configure Splunk Universal Forwarder
Centralize logs in Splunk Enterprise
Develop SPL-based security detections
Create automated security alerts
Simulate common attack techniques
Analyze attacker activity
Build a SOC monitoring dashboard
Practice detection engineering
Practice security event investigation
Key Learning Outcomes

## Through this project, I gained practical experience with:

SIEM configuration
SPL query development
Network traffic analysis
IDS deployment
Network Security Monitoring
Linux log analysis
Windows Security Event analysis
Authentication attack detection
Network reconnaissance detection
Reverse shell detection
DNS monitoring
Security alert creation
SOC dashboard development
Virtualized security lab deployment

