# SMB Brute Force Detection

SMB authentication failures were generated from Kali Linux against Windows.

## Windows Security Event:
```bash
EventCode=4625
```
## Detection
```bash
index=windows10 EventCode=4625 Logon_Type=3
| stats values(Source_Network_Address) as Source_IP values(Failure_Reason) as Failure_Reason count as Failed_Attempts by Account_Name
| where Failed_Attempts >= 3****
```
