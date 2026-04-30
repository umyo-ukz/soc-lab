# Home SOC Lab – Threat Detection with Wazuh & Suricata

## Overview
This project demonstrates the design and implementation of a home Security Operations Center (SOC) lab built to simulate and detect real-world cyberattacks. The lab integrates host-based and network-based monitoring to provide end-to-end visibility into malicious activity.

Using Wazuh (SIEM) and Suricata (IDS), I implemented log collection, custom detection rules, and attack simulations including network reconnaissance, port scanning, and brute-force attacks.

---

## Technologies Used
- Wazuh (SIEM)
- Suricata (IDS)
- Docker
- Kali Linux (Attack Machine)
- Arch Linux (Wazuh Manager Host)
- Windows 11 (Endpoints)

---

## Lab Architecture
The SOC lab is designed to simulate a real-world monitoring environment where endpoint and network data are collected and analyzed within a centralized SIEM.

### Components
- **Wazuh Manager (Docker):** Centralized log collection, analysis, and dashboard visualization  
- **Suricata IDS:** Network traffic inspection and alert generation  
- **Windows Agents:** Forward endpoint logs to Wazuh  
- **Kali Linux VM:** Simulates attack traffic  
- **Vulnerable Windows VM:** Target system for testing and analysis  

### Data Flow
Network traffic is inspected by Suricata and written to `eve.json`, which is collected by the Wazuh agent. Endpoint logs from Windows systems are also forwarded to the Wazuh manager, where events are analyzed, correlated, and visualized in the dashboard.

---

## Detection Engineering & Attack Simulations

### ICMP Reconnaissance Detection
- Developed custom Suricata rules to detect ICMP echo requests (ping traffic)  
- Tuned detection logic from broad traffic to specific ICMP types  
- Validated real-time alert pipeline into Wazuh  

**Detection Logic**
```bash
alert icmp any any -> any any (msg:"ICMP Ping Detected"; itype:8; sid:1000001; rev:1;)
```
### Nmap Scan Detection
- Integrated Emerging Threats ruleset into Suricata
- Detected SYN and aggressive scans using signature-based detection
- Confirmed visibility of reconnaissance activity within the SIEM

**Detection Logic**
```bash
alert tcp any any -> any any (msg:"ET SCAN Nmap"; flags:S; threshold:type both, track by_src, count 20, seconds 3; sid:86601; rev:1;)
```

### SSH Brute-Force Detection
- Implemented log-based detection using Wazuh
- Developed custom rules for failed authentication attempts
- Configured frequency-based alerting to detect brute-force behavior

**Detection Logic**
```xml
<rule id="100200" level="5">
  <if_sid>5710</if_sid>
  <match>Failed password</match>
  <description>SSH failed login attempt</description>
</rule>

<rule id="100201" level="12" frequency="5" timeframe="60">
  <if_matched_sid>100200</if_matched_sid>
  <description>SSH brute-force attack detected</description>
</rule>
```

### Challenges & Solutions

Suricata eve.json Not Populating
- Cause: Misconfigured output and missing rules
- Resolution: Simplified configuration and validated with test rule
  
Wazuh Agent Connection Failure
- Cause: Docker ports not exposed and firewall restrictions
- Resolution: Exposed required ports and configured firewall rules
  
JSON Decoder Overflow
- Cause: Large Windows event logs exceeding limits
- Resolution: Increased decoder limits and filtered relevant events
  
Frequency Rule Not Triggering
- Cause: Incorrect rule dependency and field extraction issues
- Resolution: Refined rule logic to rely on event frequency

Docker Certificate Errors
- Cause: Invalid certificate directory structure
- Resolution: Regenerated certificates and redeployed environment
### Impact

- Built a functional SOC lab capable of detecting real-world attack techniques
- Demonstrated end-to-end visibility from network traffic to SIEM alerts
- Developed hands-on experience in detection engineering and log analysis
  
### Planned Enhancements
- Implement active response to block malicious IPs automatically
- Integrate additional log sources (Linux systems, firewall logs, web servers)
- Incorporate threat intelligence feeds for enhanced detection
- Configure real-time alerting via email or Slack
- Expand detection coverage with vulnerability scanning integration

## Documentation
[Full project documentation (PDF)](https://drive.google.com/file/d/1NDG_Ku19nAYNSuMtqjfW7-1Iwsu__CE_/view?usp=sharing)

### Author
Nixon Rolle
