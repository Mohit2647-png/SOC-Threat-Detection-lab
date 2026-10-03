# SSH Brute Force Detection

Definition:
Detects repeated failed SSH authentication attempts that may indicate an attacker trying to guess a valid username and password.

Test performed: Multiple failed SSH login attempts were generated from Kali against Ubuntu. Ubuntu /var/log/auth.log was forwarded to Splunk and analyzed for repeated Failed password events.

## Ubuntu authentication logs:
```bash
/var/log/auth.log
```
## Detection:
```bash
index=ubuntu-auth "Failed password"
| rex "Failed password for (?:invalid user )?(?<Account_Name>\S+) from (?<Source_IP>\d{1,3}(?:\.\d{1,3}){3})"
| eval Failure_Reason="Failed password"
| stats values(Source_IP) as Source_IP values(Failure_Reason) as Failure_Reason count as Failed_Attempts by Account_Name
| where Failed_Attempts >= 3
```
