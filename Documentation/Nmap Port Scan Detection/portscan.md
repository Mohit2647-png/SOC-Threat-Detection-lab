
## Nmap Port Scanning Detection

Nmap was used from Kali Linux to perform network reconnaissance against the Windows system.
```bash
nmap -p- 192.168.20.10
```
## Splunk Detection 
```bash
index=suricata event_type=flow
| spath
| search src_ip="192.168.20.11" dest_ip="192.168.20.10"
| stats dc(dest_port) as unique_ports count as connections by src_ip dest_ip
| where unique_ports >= 5
```
