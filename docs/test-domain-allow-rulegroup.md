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
# Blocked domain
curl -v https://www.google.com --max-time 5 -o /dev/null
```

### Verify blocked domain in Firewall logs
Navigate to CloudWatch in the AWS console and search for the alert log group: `/NfwPoC/Alert`. After 2-5 minutes have passed a log entry should be visible with the following format:
```json
{
    "firewall_name": "NfwPoC-InspectionFirewall",
    "availability_zone": "eu-central-1a",
    "event_timestamp": "1714512196",
    "event": {
        "app_proto": "tls",
        "src_ip": "10.1.1.38",
        "src_port": 41994,
        "event_type": "alert",
        "alert": {
            "severity": 1,
            "signature_id": 7,
            "rev": 1,
            "signature": "not matching any TLS allowlisted FQDNs",
            "action": "blocked",
            "category": ""
        },
        "flow_id": 1470482469928521,
        "dest_ip": "172.217.18.4",
        "proto": "TCP",
        "tls": {
            "sni": "www.google.com",
            "version": "UNDETERMINED",
            "ja3": {},
            "ja3s": {}
        },
        "dest_port": 443,
        "timestamp": "2024-04-30T21:23:16.448336+0000"
    }
}
```

Verify that `event.tls.sni` is `www.google.com` and `event.alert.action` is `blocker`.
