# DNS Anomaly Detection 
Detects unusual or excessive DNS activity that may indicate suspicious automated behavior, malware communication, DNS tunneling, or other abnormal DNS usage.

Test performed: DNS queries were generated through the Ubuntu SOC network using dig. Zeek dns.log captured the DNS traffic and the events were forwarded to Splunk for analysis.

DNS testing:
```bash

dig @192.168.20.12 example.com
dig @192.168.20.12 google.com
```
Zeek log:
```bash

dns.log
```
Splunk index:
```bash
index=zeek
sourcetype=zeek:dns
```
 ## Dectection query:
```bash
index=zeek sourcetype=zeek:dns
| stats count as DNS_Queries
| where DNS_Queries >= 10
```
