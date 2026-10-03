# SSH Brute Force Detection

Multiple failed SSH authentication attempts were generated against the Ubuntu server.

## Ubuntu authentication logs:
```bash
/var/log/auth.log
```
## Detection
```bash
index=ubuntu-auth "Failed password"
| rex "Failed password for (?:invalid user )?(?<Account_Name>\S+) from (?<Source_IP>\d{1,3}(?:\.\d{1,3}){3})"
| eval Failure_Reason="Failed password"
| stats values(Source_IP) as Source_IP values(Failure_Reason) as Failure_Reason count as Failed_Attempts by Account_Name
| where Failed_Attempts >= 3
```
