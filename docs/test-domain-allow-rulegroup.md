# Test NfwPoC-DomainAllow-RuleGroup

In order to test the firewall configuration, we must test that we can reach whitelisted domains and that the rest of domains are blocked.

### Whitelisted Domain Test:
Executing the following command in the 'NfwPoC-BaseVPC-TestInstance1' Ec2 instance should return "Connection #0 to host aws.amazon.com left intact"
```bash
# Allowed domain
curl -v https://aws.amazon.com --max-time 5 -o /dev/null
```

### Blocker Domain Test:
Executing the following command in the 'NfwPoC-BaseVPC-TestInstance1' Ec2 instance should return "curl: (28) Connection timed out after 5001 milliseconds"
```bash
# Allowed domain
curl -v https://www.google.com --max-time 5 -o /dev/null
```
