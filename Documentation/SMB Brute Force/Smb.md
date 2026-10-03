# SMB Brute Force Detection

### Definition:
Detects repeated failed SMB/network authentication attempts against a Windows system, which can indicate password-guessing activity.

Test performed: Kali used NetExec to generate multiple failed SMB authentication attempts against Windows. Windows Security Event ID 4625 with Logon Type 3 was collected and analyzed in Splunk.

## Windows Security Event:
```bash
EventCode=4625
```
## Detection:
```bash
index=windows10 EventCode=4625 Logon_Type=3
| stats values(Source_Network_Address) as Source_IP values(Failure_Reason) as Failure_Reason count as Failed_Attempts by Account_Name
| where Failed_Attempts >= 3****
```
