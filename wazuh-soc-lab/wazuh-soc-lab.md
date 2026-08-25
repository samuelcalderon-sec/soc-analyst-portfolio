# Wazuh SOC / Blue Team Lab

Hands-on **SOC / Blue Team laboratory** focused on security monitoring, detection, and investigation of suspicious activity on a Windows endpoint using **Wazuh 4.12.0** and **Sysmon**.

The project reproduces, at laboratory scale, the workflow of a Tier 1 SOC analyst: telemetry generation, detection, alert triage, process and command-line analysis, event correlation, timeline reconstruction, impact assessment, MITRE ATT&CK mapping, and documentation of a defensible conclusion.

## Objective

The primary objective was to build and operate a functional security monitoring and detection environment, demonstrating the ability to:

* Generate and analyze real Windows telemetry.
* Design and validate Wazuh detection rules.
* Perform alert triage.
* Analyze parent-child process relationships.
* Investigate command lines and suspicious parameters.
* Correlate events using `ProcessGuid` and `ParentProcessGuid`.
* Reconstruct an activity timeline.
* Decode obfuscated PowerShell commands.
* Correlate endpoint telemetry with network validation.
* Map observed behavior to MITRE ATT&CK.
* Assess impact and determine a defensible classification.
* Document evidence, limitations, inferences, and conclusions.

The objective was not to build an enterprise SIEM or develop advanced Wazuh/OpenSearch administration skills, but to demonstrate practical SOC analysis and operational capabilities.

## Lab Architecture

The laboratory consisted of three main components:

```text
Kali Linux
192.168.1.21
     │
     │ TCP/4444
     ▼
Windows Endpoint
WIN10-SOC-ENDPOINT01
Sysmon + Wazuh Agent
     │
     │ Sysmon telemetry
     ▼
Wazuh SIEM
Manager + Indexer/OpenSearch + Dashboard
Ubuntu Server
```

### Components

| Component                     | Technology                      |
| ----------------------------- | ------------------------------- |
| SIEM                          | Wazuh 4.12.0                    |
| SIEM Operating System         | Ubuntu Server 24.04.4           |
| Endpoint                      | Windows                         |
| Telemetry                     | Sysmon                          |
| Agent                         | Wazuh Agent                     |
| Offensive Simulation Platform | Kali Linux                      |
| Network Validation            | Windows `netstat` + Kali Netcat |

## Telemetry

The primary telemetry source used throughout the investigation was **Sysmon Event ID 1 — Process Creation**, which was successfully ingested end-to-end into Wazuh.

The investigation analyzed fields including:

* `Image`
* `ParentImage`
* `CommandLine`
* `ParentCommandLine`
* `User`
* `ParentUser`
* `IntegrityLevel`
* `ProcessId`
* `ParentProcessId`
* `ProcessGuid`
* `ParentProcessGuid`
* `SHA256`
* UTC timestamps

A central element of the investigation was the use of `ProcessGuid` and `ParentProcessGuid` to validate parent-child process relationships. This made it possible to distinguish relationships that were actually confirmed from relationships that only appeared to belong to the same temporal sequence.

Sysmon Event ID 3 was enabled and confirmed on Windows, but it was not successfully ingested by Wazuh in this environment. Network activity was therefore validated independently using `netstat` on Windows and Netcat on Kali.

## Detection Engineering

Local Wazuh rules were configured to detect process behavior and command-line characteristics.

| Rule     | Detection                                                  |
| -------- | ---------------------------------------------------------- |
| `100100` | `notepad.exe` process creation                             |
| `100102` | PowerShell executed with `-EncodedCommand`                 |
| `100104` | PowerShell spawned by `cmd.exe`                            |
| `100105` | `cmd.exe` spawned by `notepad.exe`                         |
| `100106` | Tuning exception to reduce legitimate Wazuh Agent activity |

Rules `100102`, `100104`, and `100105` were functionally validated through evidence collected during Incident 001.

The detection logic prioritized process relationships and command-line characteristics rather than relying exclusively on file names.

## Legitimate Activity Baseline

Before executing the controlled incident scenario, legitimate activity was established as a baseline.

A normal Notepad execution produced the following process relationship:

```text
explorer.exe
    └── notepad.exe
```

The activity triggered rule `100100`, but was classified as **Benign / Expected** after additional context was analyzed: standard system path, Microsoft signature, expected parent process, user context, integrity level, and absence of suspicious child-process behavior.

This validated a fundamental SOC analysis principle:

> A correctly triggered alert does not automatically represent a security incident.

## Incident 001 — Controlled Investigation

The primary case study was an authorized simulation of suspicious behavior on the Windows endpoint.

The investigated scenario involved:

```text
Notepad
   ↓
CMD
   ↓
PowerShell
   ↓
PowerShell + -EncodedCommand
   ↓
TCP connection → Kali Linux:4444
```

The execution stages were detected through the following local rules:

```text
100105 → Notepad → CMD
100104 → CMD → PowerShell
100102 → PowerShell + EncodedCommand
```

The appearance of these indicators on the same endpoint and user within a relatively short time window justified detailed investigation during initial triage.

## Encoded PowerShell Analysis

The observed command used:

```text
-EncodedCommand
```

The content was decoded from Base64/UTF-16LE and revealed a TCP connection to:

```text
192.168.1.21:4444
```

The command used `System.Net.Sockets.TcpClient` to establish a connection, maintain it for approximately 60 seconds, and then close it.

The analysis determined that the code did not implement stream read/write operations through `NetworkStream`, remote command execution, or an interactive shell.

Therefore, the behavior was technically documented as a **controlled simulation of C2-like communication through a TCP connection**, rather than an interactive reverse shell or evidence of real C2 activity.

## Critical Process Correlation Finding

One of the most important findings of the investigation was that the telemetry **did not allow the entire sequence to be confirmed as a single continuous process-tree branch**.

Each individual relationship was validated using `ProcessGuid` / `ParentProcessGuid`:

```text
Notepad → CMD
CMD → PowerShell
PowerShell → PowerShell + EncodedCommand
```

However, the parent process GUIDs referenced at each stage did not match the intermediate processes documented in the previous stage.

Therefore, the investigation did not claim a continuous process chain that the evidence could not demonstrate.

This finding demonstrates a fundamental investigation capability:

**`ProcessId` / `ParentProcessId` alone are insufficient for reliable process-chain reconstruction; relationships must be validated using the identifiers provided by Sysmon.**

## Evidence Correlation

The investigation combined multiple sources of evidence:

1. Wazuh alerts generated from Sysmon Event ID 1.
2. `ProcessGuid` / `ParentProcessGuid` relationships.
3. Command-line data.
4. SHA256 hashes.
5. UTC timestamps.
6. Windows `netstat` validation.
7. Direct observation of the connection through the Netcat listener on Kali.

This multi-source correlation allowed the investigation to build a coherent interpretation of the observed behavior despite the network telemetry limitation in the SIEM.

## MITRE ATT&CK

The observed behavior was mapped to:

| Technique   | Name                                       | Evidence                                                                |
| ----------- | ------------------------------------------ | ----------------------------------------------------------------------- |
| `T1059.003` | Windows Command Shell                      | `cmd.exe` spawned by Notepad                                            |
| `T1059.001` | PowerShell                                 | PowerShell execution from `cmd.exe` and subsequent PowerShell instances |
| `T1027`     | Obfuscated/Compressed Files or Information | `-EncodedCommand` with Base64/UTF-16LE encoded content                  |

The TCP connection was not mapped to a specific Command and Control technique because the available evidence did not demonstrate an application-layer protocol, command exchange, or other behavior that would justify a more specific classification.

## Incident Classification

From a production perspective, the combination of:

* A non-administrative application spawning `cmd.exe`.
* `cmd.exe` spawning PowerShell.
* PowerShell executing encoded content.
* An outbound TCP connection.
* The same host and user context.

would justify a high-priority investigation.

Within this laboratory, the activity was deliberately generated and authorized.

**Final classification:**

```text
Benign / Authorized Security Validation

Disposition:
Closed — Controlled Incident Simulation
```

The detections were not classified as false positives. Rules correctly detected the behaviors they were designed to identify; the benign classification resulted from the authorized laboratory context.

## Skills Demonstrated

This project provided hands-on validation of skills related to:

* SOC Level 1 operations
* Security monitoring
* SIEM operations
* Windows security telemetry
* Sysmon analysis
* Process tree analysis
* Parent-child process correlation
* Command-line analysis
* PowerShell analysis
* Behavioral detection
* Alert triage
* Event correlation
* Timeline reconstruction
* IOC / IOA analysis
* MITRE ATT&CK mapping
* Host and network correlation
* Incident classification
* Incident response fundamentals
* Technical security documentation
* Evidence-based analysis

## Limitations

The laboratory encountered technical limitations associated with deploying Wazuh/OpenSearch on resource-constrained infrastructure.

Additionally, Sysmon Event ID 3 was confirmed on Windows but could not be successfully ingested by Wazuh in this environment. The investigation compensated for this limitation through direct and correlated validation using `netstat` and Netcat.

These limitations were documented and considered during the incident assessment.

## Conclusions

The laboratory demonstrated the ability to operate a Tier 1 SOC analytical workflow in a real laboratory environment, from telemetry generation and collection through detection, triage, correlation, reconstruction, MITRE ATT&CK mapping, and documentation of a defensible conclusion.

The primary result was not simply the successful generation of alerts, but the ability to **distinguish confirmed evidence from inference**, identify a real discontinuity in the process chain, and avoid presenting a relationship as fact when the available telemetry could not confirm it.

This demonstrated an evidence-based and behavior-oriented investigation approach rather than forcing the collected data to match a predefined attack narrative.

## Full Technical Documentation

The complete technical analysis, including methodology, environment configuration, evidence, detailed investigation, timeline, correlation, limitations, MITRE ATT&CK mapping, impact assessment, and conclusions is available in:

[**Final SOC Laboratory Report**](documentation/Informe-Final-Laboratorio-SOC.pdf)
