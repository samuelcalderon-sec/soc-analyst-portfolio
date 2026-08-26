# SOC Analyst Portfolio

Hands-on cybersecurity portfolio focused on Security Operations, Blue Team, threat detection, incident investigation, network analysis, and SIEM-based security monitoring.

This repository documents practical security investigations and controlled laboratory environments developed throughout my SOC / Blue Team training. The portfolio focuses on analyzing technical evidence, understanding attacker behavior, correlating security telemetry, reconstructing attack activity, and supporting evidence-based incident response.

The objective is to demonstrate practical analytical capabilities rather than simply list cybersecurity tools or completed training.

## Profile

Systems Engineering student focused on Cybersecurity and Security Operations.

My training has focused on developing practical capabilities across Linux, Windows, networking, malware fundamentals, SIEM, endpoint telemetry, incident investigation, and threat detection.

My investigation methodology is based on:

* Evidence-driven analysis
* Behavioral analysis
* Host and network correlation
* IOC / IOA identification
* Attack timeline reconstruction
* Detection and alert analysis
* Incident response principles
* Understanding how operating-system activity generates security telemetry
* Separating confirmed evidence, hypotheses, inferences, and conclusions

## Security Labs

### RAT C2 PCAP Analysis

Network forensics investigation using Wireshark to analyze suspicious traffic associated with a suspected Remote Access Trojan.

The investigation covered:

* Internal and external host identification
* HTTP traffic analysis
* TCP stream reconstruction
* C2 communication analysis
* Beaconing behavior
* NetSupport-related traffic
* IOC extraction
* Network activity reconstruction

**Validated capabilities:** PCAP analysis, Wireshark investigation, network forensics, C2 identification, beaconing analysis, HTTP traffic analysis, and IOC extraction.

[View the RAT C2 PCAP Analysis](./rat-c2-pcap-analysis)

### Linux Incident Analysis — SSH Brute Force & C2

Controlled Linux incident investigation simulating the compromise of an Ubuntu Server through SSH.

The investigation reconstructed the following attack chain:

`SSH Brute Force → Successful Authentication → Remote Access → Malicious Python Execution → C2 → Remote Command Execution → Sensitive Data Access`

Evidence was correlated from:

* SSH authentication logs
* Process activity
* Suspicious files
* Network connections
* Remote command execution
* Access to simulated sensitive information

**Validated capabilities:** Linux incident investigation, SSH log analysis, brute-force detection, process analysis, network correlation, C2 identification, IOC extraction, and attack timeline reconstruction.

[View the Linux Incident Analysis](./ssh-attack-c2-linux)

### Wazuh SIEM & Windows Endpoint Investigation

End-to-end SOC laboratory focused on deploying and using a Wazuh-based security monitoring environment.

The laboratory integrated a Windows endpoint with Wazuh and Sysmon telemetry to investigate endpoint activity and security events.

The investigation covered:

* Wazuh Manager, Indexer, and Dashboard architecture
* Wazuh Agent integration
* Windows endpoint monitoring
* Sysmon integration
* Windows Event Log ingestion
* Process creation telemetry
* Parent-child process relationships
* Security event analysis
* Alert investigation
* Detection rules
* Endpoint attack simulation
* Incident reconstruction
* MITRE ATT&CK-oriented analysis

**Validated capabilities:** SIEM operations, endpoint telemetry analysis, Windows security monitoring, Sysmon investigation, alert triage, detection analysis, and incident response.

[View the Wazuh SIEM Lab](./wazuh-siem-lab)

## Capabilities Validated Through Labs

The three laboratories provide direct practical evidence of the following capabilities:

**Security Monitoring and SIEM**

* Wazuh SIEM
* Log ingestion
* Security event analysis
* Alert investigation
* Detection rules
* Endpoint telemetry
* Event correlation
* Investigation timelines

**Network Security and Forensics**

* Wireshark
* PCAP analysis
* TCP stream analysis
* HTTP traffic analysis
* Network connection analysis
* C2 traffic identification
* Beaconing analysis
* IOC extraction
* Host-to-network correlation

**Linux Incident Investigation**

* SSH authentication analysis
* Brute-force investigation
* Linux process analysis
* Network connection analysis
* Suspicious script investigation
* C2 identification
* Attack-chain reconstruction

**Windows Security Monitoring**

* Windows Event Logs
* Sysmon
* Process creation telemetry
* Parent-child process analysis
* Endpoint activity investigation
* Security event correlation

## Knowledge Developed Through Training

The following areas were developed throughout the broader SOC / Blue Team roadmap. They are included as areas of technical knowledge and training, not necessarily as capabilities directly demonstrated by the three portfolio laboratories.

### Operating Systems

**Linux**

* Filesystem and permissions
* Users and privileges
* Processes
* Services
* systemd
* journalctl
* auth.log
* syslog
* cron
* Linux persistence
* Process and network correlation

**Windows**

* Windows processes
* Process trees
* Parent-child relationships
* CMD
* PowerShell
* Windows Event Logs
* Event Viewer
* Sysmon
* Services
* Scheduled Tasks
* Registry Run Keys
* Windows persistence mechanisms

## Networking

Networking concepts were studied from a SOC investigation perspective, including:

* TCP/IP
* TCP three-way handshake
* TCP vs UDP
* Ports and services
* IP addressing
* NAT
* HTTP and HTTPS
* HTTP methods
* HTTP headers
* DNS
* DNS tunneling
* Domain Generation Algorithms (DGA)
* Beaconing
* Command and Control (C2)
* Network reconnaissance
* SYN scanning
* Nmap
* Exfiltration concepts
* Process-to-network correlation

## Malware and Adversary Behavior

The training covered foundational malware analysis with an emphasis on behavior and observable evidence.

Topics included:

* Malware fundamentals
* RATs
* Trojans
* Backdoors
* C2 infrastructure
* Beaconing
* Remote Command Execution (RCE)
* Persistence
* Loaders
* Payloads
* Basic obfuscation
* Base64
* Dynamic execution
* Encoded payloads
* Host and network correlation
* IOC / IOA identification
* Legitimate versus suspicious behavior

The focus was on understanding how malicious activity interacts with the operating system and network and how that activity can become observable to defenders.

## SIEM and SOC Operations

The broader training covered the main components of a SOC monitoring workflow:

* SIEM architecture
* Log collection
* Event ingestion
* Normalization
* Indexing
* Correlation
* Detection rules
* Alert generation
* Alert triage
* IOC analysis
* Investigation timelines
* Endpoint telemetry
* Security monitoring
* Detection workflows

The Wazuh laboratory provides practical implementation and validation of these concepts.

## Incident Response and DFIR Fundamentals

The investigation methodology developed throughout the training follows a structured process:

`Observe → Identify → Correlate → Hypothesize → Validate → Reconstruct → Respond`

Particular emphasis was placed on distinguishing:

* Confirmed evidence
* Hypotheses
* Inferences
* Possible scenarios
* Evidence-supported conclusions

This approach is intended to reduce unsupported assumptions during incident investigations and maintain analytical accuracy.

Core incident response concepts covered include:

* Detection
* Triage
* Investigation
* Containment
* Eradication
* Recovery
* Incident prioritization
* Attack reconstruction
* Evidence-based decision making

## MITRE ATT&CK

MITRE ATT&CK was incorporated as a framework for understanding and classifying adversary behavior.

Training covered:

* Tactics
* Techniques
* Sub-techniques
* Initial Access
* Execution
* Persistence
* Privilege Escalation
* Defense Evasion
* Credential Access
* Discovery
* Command and Control
* Exfiltration
* Attack-chain analysis
* Mapping observed behavior to ATT&CK techniques

The framework was approached as an analytical tool for understanding attacker behavior rather than as a memorization exercise.

## Python for Cybersecurity

Python fundamentals were developed with a security-oriented focus, including:

* Variables
* Functions
* File handling
* `os`
* `sys`
* `socket`
* `requests`
* Script analysis
* Reading suspicious Python code
* Basic network communication
* Basic payload and loader concepts

## Tools and Technologies

The following tools and technologies were used or studied throughout the training:

**SIEM / Security Monitoring**

* Wazuh
* Wazuh Agent
* Wazuh Manager
* Wazuh Indexer
* Wazuh Dashboard

**Windows / Endpoint**

* Windows Event Viewer
* Windows Event Logs
* Sysmon
* Process Explorer
* Task Manager
* CMD
* PowerShell
* Sysinternals tools

**Linux**

* Ubuntu Server
* Kali Linux
* `journalctl`
* `ps`
* `ss`
* `netstat`
* `lsof`
* `systemctl`
* SSH

**Network Analysis**

* Wireshark
* PCAP analysis
* Nmap
* TCP/IP analysis
* HTTP analysis
* DNS analysis

**Programming**

* Python
* PowerShell
* Bash / Linux shell

**Security Frameworks and Concepts**

* MITRE ATT&CK
* IOC / IOA analysis
* Incident Response
* DFIR fundamentals
* Threat Detection
* Behavioral Analysis
* C2 analysis

## SOC Investigation Approach

The core principle throughout this portfolio is to investigate behavior in context rather than rely on isolated indicators.

A suspicious process, IP address, command, file, or authentication event is treated as an investigation starting point. Additional telemetry is then used to determine what actually occurred.

The general analytical model is:

`Telemetry → Detection → Triage → Investigation → Correlation → Validation → Reconstruction → Response`

This approach allows individual events to be connected into a broader attack narrative and supports more accurate incident assessment.

## Professional Focus

This portfolio is oriented toward entry-level cybersecurity positions, including:

* SOC Analyst L1 / Tier 1
* Junior SOC Analyst
* Junior Blue Team Analyst
* Security Operations Analyst
* Junior Cybersecurity Analyst
* Security Monitoring Analyst

The projects are intended to demonstrate practical investigation skills, analytical reasoning, and familiarity with real security telemetry in controlled environments.

## Repository Structure

```text
soc-analyst-portfolio/
│
├── rat-c2-pcap-analysis/
│   ├── README.md
│   └── Incident-Analysis-C2-Traffic-PCAP.pdf
│
├── ssh-attack-c2-linux/
│   ├── README.md
│   └── linux-incident-ssh-c2-analysis.pdf
│
├── wazuh-siem-lab/
│   ├── README.md
│   └── ...
│
└── README.md
```

Each laboratory contains a concise technical README. Where applicable, the repository also includes a complete investigation report containing the detailed methodology, evidence, analysis, and conclusions.

## Contact

**Samuel Calderón Candela**

Systems Engineering Student | Cybersecurity | SOC / Blue Team

Armenia, Colombia

[LinkedIn](https://www.linkedin.com/in/samuel-calderon-candela-b792a836a/)

## Disclaimer

All attack simulations and security investigations documented in this repository were performed in controlled laboratory environments for educational and defensive security purposes.

The techniques and tools demonstrated should only be used on systems and environments where explicit authorization has been granted.

