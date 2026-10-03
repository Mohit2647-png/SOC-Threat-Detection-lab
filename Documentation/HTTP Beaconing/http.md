# HTTP Beaconing Detection

Zeek was used to monitor HTTP traffic generated inside the SOC network.

##Zeek HTTP log:
```bash

http.log
```
Splunk source:
```bash
index=suricata "event_type"="http" | spath | stats count as HTTP_Requests by src_ip dest_ip dest_port
http.http_method http.status
```
HTTP traffic was analyzed for repeated communication patterns that could indicate automated or beacon-like activity.
