````md
# Cloud-Native SIEM & Vulnerability Management Lab

## Overview

This project demonstrates the deployment of a cloud-native Security Information and Event Management (SIEM) environment using **Wazuh** hosted on **AWS EC2** to monitor a **Windows 11** endpoint in real time.

The environment replicates core SOC monitoring and vulnerability management workflows used in enterprise environments, bridging cloud infrastructure with endpoint security telemetry.

---

## Architecture & Data Flow

The system uses a client-server architecture secured through AWS Security Groups and TLS-encrypted agent communication.

### Infrastructure Components

- **Manager (Cloud):**
  - Wazuh Manager
  - Wazuh Indexer
  - Wazuh Dashboard
  - Hosted on Ubuntu 22.04 LTS (AWS EC2 t3.medium)

- **Agent (Endpoint):**
  - Windows 11 machine
  - Wazuh Agent v4.7.5

- **Data Pipeline:**
  - Endpoint telemetry securely transmitted over TLS-encrypted channels
  - Agent communication handled through ports 1514 and 1515

---

## Architecture Diagram

![Architecture](architecture/architecture-diagram.png)

---

## Technologies Used

- AWS EC2
- Ubuntu 22.04 LTS
- Wazuh v4.7.5
- Windows 11
- PowerShell
- Linux CLI
- AWS Security Groups

---

## AWS Security Group Configuration

| Port | Protocol | Purpose | Source |
|---|---|---|---|
| 22 | TCP | SSH Management | Restricted to My IP |
| 443 | TCP | Wazuh Dashboard Access | 0.0.0.0/0 |
| 1514 | TCP | Agent Event Data | 0.0.0.0/0 |
| 1515 | TCP | Agent Registration | 0.0.0.0/0 |

---

## Deployment Process

### Phase 1 — Cloud Infrastructure

- Deployed Ubuntu 22.04 EC2 instance in AWS us-east-1
- Configured AWS Security Groups
- Expanded EBS storage from 8 GiB to 20 GiB
- Installed Wazuh Manager, Indexer, and Dashboard

### Phase 2 — Endpoint Enrollment

- Installed Wazuh Agent on Windows 11
- Registered endpoint with Wazuh Manager
- Verified successful telemetry transmission

### Phase 3 — Vulnerability Detection

- Enabled Vulnerability Detector module
- Synchronized CVE databases
- Performed baseline vulnerability scans

### Phase 4 — Threat Monitoring

- Simulated brute-force login attempts
- Monitored alert generation
- Verified MITRE ATT&CK mappings

---

## Deployment Scripts

### Windows Agent Installation (PowerShell)

```powershell
# Deploy Wazuh Agent with Manager IP
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.7.5-1.msi -OutFile ${env.tmp}\wazuh-agent.msi

msiexec.exe /i ${env.tmp}\wazuh-agent.msi /q `
WAZUH_MANAGER='YOUR_AWS_IP' `
WAZUH_REGISTRATION_SERVER='YOUR_AWS_IP'

NET START WazuhSvc
````

### Linux Filesystem Expansion

```bash
# Expand filesystem after EBS volume scaling
sudo growpart /dev/nvme0n1 1
sudo resize2fs /dev/nvme0n1p1
```

---

## Vulnerability Findings

### Baseline Assessment

Initial scans identified multiple security vulnerabilities on the Windows 11 endpoint.

| Severity | Count |
| -------- | ----- |
| Critical | 2     |
| High     | 10    |
| Medium   | 11    |
| Low      | 1     |

### Key Findings

* Python 3.10.11 Vulnerabilities

  * CVE-2024-6232
  * CVE-2022-37454

* MySQL Server 5.0

  * End-of-Life software
  * Multiple known CVEs

* Configuration weaknesses identified through Security Configuration Assessment (SCA)

---

## Remediation Results

The attack surface was reduced by:

* Updating vulnerable Python packages
* Removing End-of-Life MySQL Server software
* Reviewing insecure configurations identified by SCA

This demonstrated the full vulnerability management lifecycle:

* Detection
* Prioritization
* Remediation
* Validation

---

## Attack Simulation

### Brute Force Simulation

A brute-force attack simulation was conducted using repeated failed login attempts against the Windows endpoint.

### Detection Results

* High-severity alerts successfully triggered
* Alert Level: 10
* MITRE ATT&CK Technique:

  * T1110 — Brute Force

---

## Security Monitoring Features

* Vulnerability Detection
* MITRE ATT&CK Mapping
* File Integrity Monitoring (FIM)
* Security Configuration Assessment (SCA)
* Real-time Log Monitoring
* Threat Alerting

---

## Screenshots

### Active Wazuh Agent

![Agent](screenshots/active-agent.png)

### Vulnerability Dashboard

![Vulnerabilities](screenshots/vulnerabilities-dashboard.png)

### Critical Vulnerabilities

![Critical](screenshots/critical-vulnerabilities.png)

### AWS Security Group Rules

![Security Groups](screenshots/security-group-rules.png)

### Security Configuration Assessment

![SCA](screenshots/sca-results.png)

---

## Troubleshooting & Challenges

### Storage Constraints

* Default 8 GiB storage was insufficient for Wazuh indexing and CVE databases
* Expanded EBS volume to 20 GiB

### Agent Connectivity Issues

* Agent initially showed "Offline"
* Opened ports 1514 and 1515 in AWS Security Groups

### Dashboard Lockout

* HTTPS access was accidentally removed during firewall modification
* Restored port 443 access

### High CPU Usage

* Vulnerability feed synchronization caused CPU spikes
* Monitored `ossec.log` during indexing operations

### Service Initialization Delays

* Dashboard displayed "Server is not ready yet"
* Restarted services and monitored synchronization state

---

## Skills Demonstrated

### Cloud Security

* AWS EC2 Management
* Security Group Hardening
* EBS Volume Scaling

### SIEM & SOC Operations

* Real-time Threat Monitoring
* Alert Analysis
* MITRE ATT&CK Mapping

### Vulnerability Management

* CVE Identification
* Risk Prioritization
* Remediation Planning

### System Administration

* Linux Administration
* PowerShell Automation
* Service Troubleshooting

---

## Lessons Learned

* Resource planning is critical for SIEM deployments
* Security Group misconfigurations can interrupt telemetry pipelines
* CLI-based log analysis is essential during dashboard failures
* Vulnerability management requires continuous remediation validation

---

## Future Improvements

* Integrate Suricata IDS
* Configure Active Response
* Add Email Alerting
* Deploy Multiple Endpoints
* Implement Sigma Rules
* Integrate Threat Intelligence Feeds

---

## Project Status

✅ Completed Successfully

---

## Author

**Ram**
MCA Cybersecurity & Forensics Student

---

## Disclaimer

This project was created in a controlled lab environment for educational and research purposes only.

```
```
