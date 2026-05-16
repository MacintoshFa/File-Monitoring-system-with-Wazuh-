# File Integrity Monitoring with Wazuh

## Overview

This project demonstrates the implementation of a File Integrity Monitoring (FIM) solution using Wazuh in a VirtualBox homelab environment. The objective is to monitor critical files and directories for unauthorized changes, detect suspicious activity, and generate security alerts through the Wazuh Dashboard.

File Integrity Monitoring is an important security control used to detect:

* Unauthorized file modifications
* Malware activity
* Configuration changes
* Privilege escalation attempts
* Insider threats
* Policy violations

The project uses Ubuntu Server as the Wazuh Manager ,Windows 11 and Ubuntu Desktop as the monitored endpoint.

---

# Lab Environment

| System         | Role                      |
| -------------- | ------------------------- |
| Ubuntu Server  | Wazuh Server              |
| Ubuntu Desktop | Monitored Endpoint        |
| Kali Linux     | Attack Simulation Machine |

Network Type: Bridged Adapter

---

# Hardware Requirements

| Component      | Requirement         |
| -------------- | ------------------- |
| CPU            | Dual-Core Processor |
| RAM            | 8 GB Minimum        |
| Storage        | 80 GB               |
| Hypervisor     | VirtualBox          |
| Virtualization | Enabled in BIOS     |

---

# Software Requirements

* VirtualBox
* Ubuntu Server
* Ubuntu Desktop
* Kali Linux
* Wazuh
* Linux CLI Tools
* Windows 11

---

# Project Objectives

* Deploy Wazuh Server on Ubuntu Server
* Deploy Wazuh Agent on Ubuntu Desktop
* Configure File Integrity Monitoring
* Monitor sensitive directories and files
* Detect unauthorized changes
* Generate alerts in Wazuh Dashboard
* Simulate attacks and file modifications
* Analyze generated alerts

---

# Virtual Machine Setup

## Ubuntu Server

Recommended configuration:

* RAM: 4 GB
* CPU: 2 Cores
* Storage: 50 GB
* Network Adapter: Bridged Adapter

---

## Ubuntu Desktop

Recommended configuration:

* RAM: 4 GB
* CPU: 2 Cores
* Storage: 40 GB
* Network Adapter: Bridged Adapter

---

## Kali Linux

Recommended configuration:

* RAM: 2 GB
* CPU: 2 Cores
* Storage: 30 GB
* Network Adapter: Bridged Adapter

---

# Installing Wazuh Server

Update Ubuntu Server:

```bash
sudo apt update && sudo apt upgrade -y
```

Download Wazuh installer:

```bash
curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
```

Run installation:

```bash
sudo bash wazuh-install.sh -a
```

This installs:

* Wazuh Manager
* Wazuh Dashboard
* Wazuh Indexer
* Filebeat

---

# Access Wazuh Dashboard

Open browser:

```text
https://WAZUH_SERVER_IP
```

Login with generated credentials displayed after installation.

---

# Verify Wazuh Services

Check Wazuh manager:

```bash
sudo systemctl status wazuh-manager
```

---

# Deploy Wazuh Agent on Ubuntu Desktop

Install agent:

```bash
sudo apt install wazuh-agent -y
```

Edit configuration:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Configure Wazuh manager address:

```xml
<server>
  <address>WAZUH_SERVER_IP</address>
</server>
```

Enable and start agent:

```bash
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

Verify status:

```bash
sudo systemctl status wazuh-agent
```

---

# Register Agent with Wazuh Server

On Wazuh Server:

```bash
sudo /var/ossec/bin/manage_agents
```

Select:

```text
A - Add Agent
```

Enter:

* Agent name
* Agent IP address

Extract key:

```text
E - Extract key
```

Copy generated key.

On Ubuntu Desktop:

```bash
sudo /var/ossec/bin/manage_agents
```

Select:

```text
I - Import key
```

Paste generated key.

Restart agent:

```bash
sudo systemctl restart wazuh-agent
```

---

# Verify Agent Enrollment

Navigate to:

```text
Wazuh Dashboard → Agents
```

Expected status:

```text
Active
```

---

# Configure File Integrity Monitoring

Edit Wazuh agent configuration:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Locate the syscheck section.

Example configuration:

```xml
<syscheck>
  <frequency>43200</frequency>
  <directories check_all="yes" realtime="yes">/etc,/var/www,/home</directories>
</syscheck>
```

Explanation:

* `frequency` defines scan interval in seconds
* `check_all="yes"` monitors all file attributes
* `realtime="yes"` enables immediate change detection
* `/etc` monitors Linux configuration files
* `/home` monitors user directories
* `/var/www` monitors web application files

---

# Restart Wazuh Agent

Apply configuration changes:

```bash
sudo systemctl restart wazuh-agent
```

---

# Verify FIM Configuration

Check Wazuh logs:

```bash
sudo tail -f /var/ossec/logs/ossec.log
```

Expected output:

```text
Starting syscheck scan.
```

---

# File Monitoring Test

Create a monitored file:

```bash
touch ~/testfile.txt
```

Modify the file:

```bash
echo "Security test" >> ~/testfile.txt
```

Delete the file:

```bash
rm ~/testfile.txt
```

Expected Result:

* Wazuh detects file creation
* Wazuh detects modification
* Wazuh detects deletion
* Alerts appear in dashboard

---

# Simulate Unauthorized Changes

Modify a monitored system file:

```bash
sudo echo "Unauthorized change" >> /etc/hosts
```

Expected Result:

* File modification alert generated
* Alert visible in dashboard
* Event severity assigned

---

# Monitoring Alerts in Wazuh Dashboard

Navigate to:

```text
Security Events
```

FIM alerts display:

* File path
* Event type
* Timestamp
* User account
* File checksum changes
* Alert severity

---

# Example FIM Alert

```json
{
  "rule": {
    "level": 7,
    "description": "File modified"
  },
  "syscheck": {
    "path": "/etc/hosts",
    "event": "modified"
  }
}
```

---

# Common FIM Event Types

| Event    | Description           |
| -------- | --------------------- |
| added    | New file created      |
| modified | Existing file changed |
| deleted  | File removed          |
| renamed  | File name changed     |

---

# Attack Simulation

## Simulate Suspicious File Activity

From Kali Linux, access shared directories or monitored folders and create suspicious files.

Example:

```bash
echo "malicious activity" > suspicious.txt
```

Expected Result:

* Wazuh generates FIM alert
* Dashboard records file activity
* Alerts contain event details

---

# Benefits of File Integrity Monitoring

* Detects unauthorized file changes
* Monitors critical system files
* Improves endpoint visibility
* Supports compliance requirements
* Helps detect malware persistence
* Provides real time monitoring

---

# Challenges Encountered

* Configuring syscheck correctly
* Monitoring large directories efficiently
* Troubleshooting agent communication
* Managing alert noise and false positives

---

# Skills Demonstrated

* SIEM deployment
* Endpoint monitoring
* Linux administration
* File Integrity Monitoring
* Log analysis
* Threat detection
* Security monitoring
* VirtualBox networking

---

# Screenshots to Include

* Wazuh dashboard overview
* Active Wazuh agent
* Syscheck configuration
* File creation alert
* File modification alert
* File deletion alert
* Wazuh security events page

---

# Future Improvements

* Monitor Windows file systems
* Add malware detection rules
* Integrate Suricata alerts
* Configure email notifications
* Create automated incident response actions
* Add centralized log retention

---

# Repository Structure

```text
wazuh-fim-project/
├── screenshots/
├── configs/
│   └── ossec.conf
├── logs/
├── attack-simulation/
├── README.md
└── documentation/
```

---

# Resume Project Description

Built a File Integrity Monitoring solution using Wazuh and Ubuntu systems in a VirtualBox lab environment. Configured real time monitoring of critical directories, detected unauthorized file modifications, and analyzed security alerts through the Wazuh dashboard.

---

# Author

Your Name

Cybersecurity Student | SOC Analyst Aspirant | Homelab Enthusiast
