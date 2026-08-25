# RAT C2 PCAP Analysis

## Overview

This lab focuses on the analysis of network traffic associated with a suspected Remote Access Trojan (RAT) using a PCAP file and Wireshark.

The investigation simulates a SOC analyst reviewing captured network traffic to identify suspicious communication, determine the compromised host, identify the external C2 infrastructure, and reconstruct the observed communication pattern.

The analysis focuses on network-based evidence rather than malware reverse engineering.

## Investigation Objective

The main objectives of the investigation were to:

- Identify suspicious network communications.
- Determine the internal host involved in the activity.
- Identify the external C2 server.
- Analyze the communication pattern between the host and the remote server.
- Identify indicators of compromise (IOCs).
- Determine whether the traffic is consistent with C2 activity.
- Reconstruct the relevant network activity from the PCAP.

## Scenario

The captured traffic contains communication between an internal Windows host and an external IP address associated with suspected RAT activity.

The relevant network information identified during the investigation was:

| Indicator | Value |
|---|---|
| Internal Network | `10.2.28.0/24` |
| Suspected Host | `10.2.28.88` |
| External C2 IP | `45.131.214.85` |
| Protocol | HTTP |
| User-Agent | `NetSupport Manager/1.3` |
| Server | `NetSupport Gateway/1.92` |
| HTTP Endpoint | `/fakeurl.htm` |
| Observed Commands | `CMD=POLL`, `CMD=ENCD` |

## Methodology

The investigation was conducted using Wireshark and followed a network-forensics workflow:

1. Identify internal hosts communicating with external systems.
2. Investigate suspicious external destinations.
3. Filter and inspect HTTP traffic.
4. Follow TCP streams to reconstruct communications.
5. Analyze HTTP requests, responses, headers, and parameters.
6. Identify recurring communication patterns.
7. Extract relevant IOCs.
8. Correlate the observed traffic with known C2 characteristics.
9. Reconstruct the observed activity and determine its security relevance.

## Key Findings

The investigation identified repeated HTTP communication between the internal host `10.2.28.88` and the external IP `45.131.214.85`.

The traffic included HTTP POST requests to `/fakeurl.htm` and parameters such as `CMD=POLL` and `CMD=ENCD`.

The communication showed a recurring pattern consistent with beaconing, where the internal host periodically communicated with the same external infrastructure.

The observed `NetSupport Manager/1.3` User-Agent and `NetSupport Gateway/1.92` server information provided additional context for identifying the traffic as associated with NetSupport-based remote access activity.

The combination of the repeated communication pattern, external destination, HTTP POST traffic, and NetSupport-related indicators provided evidence of a suspicious C2 communication channel.

## Indicators of Compromise

| Type | Indicator |
|---|---|
| Internal IP | `10.2.28.88` |
| External IP | `45.131.214.85` |
| HTTP Endpoint | `/fakeurl.htm` |
| User-Agent | `NetSupport Manager/1.3` |
| Server | `NetSupport Gateway/1.92` |
| Parameters | `CMD=POLL`, `CMD=ENCD` |

## SOC Analyst Skills Demonstrated

- PCAP analysis
- Wireshark investigation
- Network traffic analysis
- C2 traffic identification
- Beaconing analysis
- TCP stream analysis
- HTTP traffic investigation
- IOC extraction
- Network-based incident investigation
- Attack activity reconstruction

## Conclusion

The PCAP analysis identified suspicious and recurring HTTP communication between the internal host `10.2.28.88` and the external infrastructure `45.131.214.85`.

The observed communication pattern and NetSupport-related indicators are consistent with C2 activity associated with a remote access tool.

The investigation demonstrates how a SOC analyst can use network telemetry to identify suspicious communications, extract actionable indicators, and reconstruct attacker-related activity from packet captures.

## Full Investigation Report

The complete investigation, including detailed evidence and analysis, is available in the accompanying report:

**`Incident-Analysis-C2-Traffic-PCAP.pdf`**

## Tools

- Wireshark
- PCAP network capture
