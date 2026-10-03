# HTTP Beaconing Detection

### Definition:
Detects repeated or periodic HTTP communication between a host and a remote destination that may indicate automated communication, command-and-control activity, or malware beaconing.

Test performed: HTTP traffic was generated in the lab and monitored using Zeek http.log. The logs were forwarded to Splunk for analysis of repeated HTTP communication patterns.

## Zeek HTTP log:
```bash

http.log
```
Splunk source:
```bash
index=suricata "event_type"="http" | spath | stats count as HTTP_Requests by src_ip dest_ip dest_port
http.http_method http.status
```
HTTP traffic was analyzed for repeated communication patterns that could indicate automated or beacon-like activity.
