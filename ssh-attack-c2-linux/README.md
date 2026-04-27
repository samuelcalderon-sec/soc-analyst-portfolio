Linux Incident Analysis – SSH Attack & C2 Activity

## 📌 Overview

This project simulates a real-world security incident in a Linux environment, where an attacker gains unauthorized access via SSH, executes malicious code, and establishes a Command and Control (C2) channel.

The investigation focuses on correlating logs, processes, and network connections to reconstruct the attack timeline.



## 🎯 Objective

* Detect unauthorized SSH access
* Identify attacker activity post-compromise
* Analyze process execution and persistence
* Detect C2 communication
* Extract Indicators of Compromise (IoCs)



## 🖥️ Environment

* Attacker machine: Kali Linux
* Victim machine: Ubuntu Server 24.04
* Service exposed: SSH (OpenSSH)
* Network: Controlled lab environment



## ⏱️ Attack Timeline

### 1. Initial Access (Brute Force)

* Multiple failed SSH login attempts detected
* Successful login from attacker IP

➡️ Indicates credential compromise



### 2. Privilege Access

* Attacker obtained shell access
* Evidence of elevated privileges



### 3. Malicious Execution

* Suspicious script created:

id="q1k2zj"
/tmp/update.py

* Executed via:

id="t3x7na"
python3


➡️ Use of `/tmp` suggests evasion technique



### 4. C2 Communication Established

* Outbound connection detected:

id="7m7n4k"
port 4444


* Linked to malicious process

➡️ Active Command & Control channel



### 5. Remote Command Execution

* Attacker executed commands remotely via C2



### 6. Data Access / Exfiltration

* Accessed sensitive file:

id="0d62vb"
/tmp/contrasenias.txt


➡️ Potential data exfiltration



## 🔍 Evidence Collected

* SSH logs (`journalctl -u ssh`)
* Running processes (`ps aux`)
* Active connections (`ss -tnp`)
* Suspicious files in `/tmp`



## 🚨 Indicators of Compromise (IoCs)

* Suspicious IP (attacker machine)
* Non-standard port usage: `4444`
* Malicious script: `/tmp/update.py`
* Suspicious process: `python3`
* Unauthorized SSH access
* Outbound C2 connection



## 🧠 Attack Summary

The attacker gained access via SSH brute force, executed a malicious script, and established a reverse connection to maintain remote control.

The compromise demonstrates a full attack chain:

* Initial access
* Execution
* Command & Control
* Data access



## 🛡️ Recommendations

* Implement SSH hardening (keys, disable root login)
* Deploy fail2ban to prevent brute force
* Monitor logs continuously
* Restrict outbound connections
* Implement EDR solutions
* Monitor execution in sensitive directories (`/tmp`)

## 📎 Full Report
[Download Full Incident Report](./linux-incident-ssh-c2-analysis.pdf)
