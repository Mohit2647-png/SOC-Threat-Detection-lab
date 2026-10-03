# HTTP Beaconing Detection

Zeek was used to monitor HTTP traffic generated inside the SOC network.

##Zeek HTTP log:
```bash

http.log
```
Splunk source:

index=zeek sourcetype=zeek:http

HTTP traffic was analyzed for repeated communication patterns that could indicate automated or beacon-like activity.
