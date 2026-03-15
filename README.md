# SPLUNK-SIEM-HOMELAB
# SIEM Security Monitoring Lab with Splunk

## Project Overview

This project demonstrates a **Security Information and Event Management (SIEM) lab environment** built using **Splunk Enterprise**. The lab simulates real-world attack scenarios such as **SSH brute force attacks and privilege escalation** against a Linux server and detects them using custom detection rules, alerts, dashboards, and threat intelligence enrichment.

The project demonstrates key **Security Operations Center (SOC)** capabilities including:

* Log ingestion
* Detection engineering
* Alert creation
* MITRE ATT&CK mapping
* Threat intelligence enrichment
* Incident response investigation

---

# Architecture

The SIEM lab environment consists of a Windows attacker machine, a Linux target system, and Splunk Enterprise for centralized log monitoring.

```
Windows Host (Attacker)
        ↓
SSH Brute Force Attack
        ↓
Linux Server (Target)
        ↓
/var/log/auth.log
        ↓
Splunk Universal Forwarder
        ↓
Splunk Enterprise SIEM
        ↓
Detection Rules → Alerts → SOC Investigation
```

---

# Technologies Used

* Splunk Enterprise
* Splunk Universal Forwarder
* Ubuntu Linux Virtual Machine
* Windows Host System
* SSH
* MITRE ATT&CK Framework

---

# Lab Environment Setup

## 1. Install Splunk Enterprise

Download Splunk Enterprise from:

https://www.splunk.com/en_us/download.html

Install on the **Windows host machine**.

Start Splunk:

```
http://localhost:8000
```

---

## 2. Create Linux Target System

Create a **Linux virtual machine** (Ubuntu recommended) using:

* VirtualBox
* VMware

Ensure SSH is enabled:

```
sudo apt update
sudo apt install openssh-server
```

Check SSH service:

```
sudo systemctl status ssh
```

---

## 3. Install Splunk Universal Forwarder

Download Splunk Universal Forwarder for Linux.

Install:

```
sudo dpkg -i splunkforwarder.deb
```

Start the forwarder:

```
sudo /opt/splunkforwarder/bin/splunk start
```

Connect it to Splunk Enterprise:

```
sudo /opt/splunkforwarder/bin/splunk add forward-server SPLUNK_SERVER_IP:9997
```

---

## 4. Configure Log Forwarding

Forward Linux authentication logs:

```
sudo /opt/splunkforwarder/bin/splunk add monitor /var/log/auth.log
```

This sends authentication logs to Splunk for monitoring.

---

# Attack Simulation

## SSH Brute Force Attack

From the **Windows host machine**, perform multiple SSH login attempts:

```
ssh username@linux-ip
```

Enter incorrect passwords multiple times to generate failed login events.

These events will appear in:

```
/var/log/auth.log
```

---

## Privilege Escalation Simulation

After logging into the Linux system, execute:

```
sudo -l
sudo su
```

This generates **sudo events** that Splunk can detect.

---

# Detection Engineering

Custom detection rules were developed in Splunk to detect malicious behavior.

---

## SSH Brute Force Detection

```
index=* "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| bin _time span=1m
| stats count by _time src_ip
| where count > 5
```

Detects multiple failed SSH login attempts from the same IP.

---

## Privilege Escalation Detection

```
index=* sudo
```

Detects sudo activity indicating possible privilege escalation.

---

## Correlation Detection Rule

The SIEM also detects multi-stage attack patterns:

```
Brute Force Attempts
        ↓
Successful Login
        ↓
Privilege Escalation
```

This helps identify **account compromise scenarios**.

---

# Alerts

Splunk alerts were created for the following detections:

| Alert Name                     | Description                                |
| ------------------------------ | ------------------------------------------ |
| SSH Brute Force Detection      | Detects repeated failed login attempts     |
| Privilege Escalation Detection | Detects sudo command activity              |
| SSH Compromise Detection       | Detects successful login after brute force |

Alerts trigger automatically when suspicious activity occurs.

---

# MITRE ATT&CK Mapping

Detections were mapped to MITRE ATT&CK techniques.

| Detection            | Technique | Tactic               |
| -------------------- | --------- | -------------------- |
| SSH Brute Force      | T1110     | Credential Access    |
| Valid Account Login  | T1078     | Initial Access       |
| Privilege Escalation | T1548     | Privilege Escalation |

---

# Threat Intelligence Enrichment

Threat intelligence lookups were integrated to identify known malicious IP addresses.

Example lookup query:

```
| makeresults
| eval src_ip="ATTACKER_IP"
| lookup alienvault_lookup ip as src_ip OUTPUT reputation country
```

This allows the SIEM to determine if attacker infrastructure appears in threat intelligence feeds.

---

# Dashboard

A Splunk dashboard was created to visualize security activity.

Dashboard panels include:

* Failed SSH login attempts over time
* Top attacker IP addresses
* Login success vs failure
* Privilege escalation activity
* MITRE ATT&CK technique distribution

---

# Incident Response Playbook

An incident response workflow was created to simulate SOC analyst investigation.

Investigation steps include:

1. Identify attacker IP
2. Check successful login events
3. Investigate privilege escalation activity
4. Verify attacker IP using threat intelligence
5. Block attacker IP

Full playbook available here:

```
playbook/incident_response.txt
---

# Key Skills Demonstrated

* SIEM deployment and configuration
* Log ingestion and monitoring
* Detection engineering
* Security alert creation
* Threat intelligence integration
* MITRE ATT&CK mapping
* SOC incident investigation

---

# Future Improvements

* Integrate Windows event logs
* Add RDP brute force detection
* Implement automated threat intelligence feeds
* Integrate SOAR for automated response
* Simulate additional adversary techniques

---

# Author

Cybersecurity SIEM Lab Project
Built using Splunk Enterprise for SOC monitoring and detection engineering practice.
