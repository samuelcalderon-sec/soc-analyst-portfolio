# Linux Incident Analysis – SSH Brute Force & C2

## Overview

This lab simulates a Linux security incident in a controlled virtual environment, focusing on the investigation of an SSH-based compromise and the subsequent establishment of a Command-and-Control (C2) channel.

The investigation follows a SOC-oriented approach, correlating authentication logs, process activity, and network connections to reconstruct the attack chain from initial access to remote command execution and access to sensitive information.

## Investigation Objective

The main objectives of the investigation were to:

- Identify the initial access vector.
- Detect and analyze SSH brute-force activity.
- Identify the successful authentication associated with the compromise.
- Investigate suspicious processes and files on the victim system.
- Identify the C2 communication channel.
- Correlate processes with network connections.
- Identify Indicators of Compromise (IoCs).
- Reconstruct the attack timeline.
- Recommend security controls to mitigate similar attacks.

## Lab Environment

| Component | Description |
|---|---|
| Attacker | Kali Linux |
| Victim | Ubuntu Server 24.04 |
| Network | Controlled virtual environment |
| Remote Service | OpenSSH Server |
| C2 Port | `4444` |

Both machines were connected to the same isolated virtual network, allowing the attack scenario and subsequent investigation to be performed in a controlled environment.

## Attack Chain

The investigation reconstructed the following sequence:

`SSH Brute Force → Successful Authentication → Remote Access → Malicious Python Execution → C2 Channel → Remote Command Execution → Sensitive File Access`

The attacker initially performed multiple failed SSH authentication attempts against the victim system. A subsequent successful authentication from the same source indicated the compromise of valid credentials.

After gaining access, the attacker executed a Python script located at `/tmp/update.py`. The process established an outbound TCP connection to the attacker machine on port `4444`, creating a C2 communication channel.

Through this channel, remote commands were executed on the compromised system, including access to sensitive information stored in the `/tmp` directory.

## Investigation Methodology

The investigation was based on host and network telemetry available within the simulated environment.

The following evidence sources and commands were used:

- `journalctl -u ssh` — SSH authentication log analysis.
- `ps aux` — Process identification and analysis.
- `ss -tnp` — Network connection and process correlation.
- File analysis — Investigation of suspicious files and accessed data.

The investigation correlated these sources to establish relationships between authentication events, process execution, network connections, and remote activity.

## Key Findings

The investigation identified:

- Multiple failed SSH authentication attempts consistent with brute-force activity.
- A subsequent successful authentication from the attacker machine.
- Execution of `python3` associated with `/tmp/update.py`.
- An established TCP connection to the attacker machine on port `4444`.
- Direct association between the network connection and the `python3` process.
- Remote command execution through the established C2 channel.
- Access to `/tmp/contrasenias.txt`, containing simulated sensitive information.

These findings allowed the attack sequence to be reconstructed from initial access through post-compromise activity.

## Indicators of Compromise

| Type | Indicator |
|---|---|
| Attacker IP | IP address of the attacker machine |
| C2 Port | `4444` |
| Suspicious Process | `python3` |
| Suspicious File | `/tmp/update.py` |
| Accessed Data | `/tmp/contrasenias.txt` |
| Network Activity | Outbound TCP connection associated with `python3` |

## SOC Analyst Skills Demonstrated

- Linux incident investigation
- SSH authentication log analysis
- Brute-force detection
- Process analysis
- Network connection analysis
- Host and network evidence correlation
- C2 identification
- IOC extraction
- Attack timeline reconstruction
- Incident response analysis
- Security control recommendations

## Security Recommendations

The investigation highlights several controls that can reduce the risk of similar incidents:

- Harden SSH authentication and restrict authentication attempts.
- Prefer public key authentication over passwords.
- Disable direct root login.
- Implement tools such as Fail2Ban.
- Monitor SSH authentication logs through a SIEM.
- Monitor suspicious process execution and script activity.
- Restrict unnecessary outbound network connections.
- Implement access controls and auditing for sensitive files.

## Conclusion

This lab demonstrates how a SOC analyst can reconstruct a Linux compromise by correlating authentication logs, process activity, and network connections.

The investigation identified the progression from SSH brute-force activity to successful authentication, malicious code execution, C2 establishment, remote command execution, and access to sensitive information.

The exercise demonstrates the importance of analyzing host and network telemetry together rather than relying on a single indicator. Correlating these sources provides the context required to understand attacker behavior, identify IoCs, and support incident response decisions.

## Full Investigation Report

The complete investigation, including detailed evidence and analysis, is available in the accompanying report:

**`linux-incident-ssh-c2-analysis.pdf`**

## Tools

- Ubuntu Server 24.04
- Kali Linux
- OpenSSH
- `journalctl`
- `ps`
- `ss`
