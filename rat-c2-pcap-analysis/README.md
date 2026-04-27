🛡️ RAT C2 Traffic Analysis (PCAP Investigation)

📌 Overview

This project presents a network traffic analysis performed on a PCAP file following a SIEM alert indicating suspicious outbound communication.

The investigation focuses on identifying potential Command and Control (C2) activity associated with a Remote Access Trojan (RAT), specifically NetSupport Manager.



🎯 Objective

* Identify the compromised host within the internal network
* Analyze suspicious outbound traffic
* Detect C2 communication patterns
* Extract Indicators of Compromise (IoCs)
* Reconstruct attacker activity



🌐 Environment

* Network: 10.2.28.0/24
* Analysis tool: Wireshark
* Protocols observed: DNS, HTTP, TCP, SMB2
* Suspected malware: NetSupport Manager RAT



🔍 Investigation Process

1. Initial Traffic Inspection

* Opened PCAP in Wireshark
* Identified high traffic volume from host **10.2.28.88**
* External suspicious IP detected: **45.131.214.85**



2. Suspicious Communication Analysis

* Repeated HTTP POST requests observed
* Endpoint: `/fakeurl.htm`
* Content-Type: `application/x-www-form-urlencoded`

➡️ Indicates potential data exfiltration



3. Beaconing Detection

* Continuous periodic communication between:

  * Internal host: 10.2.28.88
  * External IP: 45.131.214.85

➡️ Pattern consistent with C2 beaconing



4. Deep Packet Inspection

Using "Follow TCP Stream":

* User-Agent identified:


  NetSupport Manager/1.3

* Server response:


  NetSupport Gateway/1.92


➡️ Confirms RAT infrastructure



5. Command & Control Behavior

Observed parameters:

* `CMD=POLL` → host requesting commands
* `CMD=ENCD` → encoded data exchange

➡️ Active remote control channel established


6. Host Identification

* High packet volume (~550 packets)
* Persistent connection with attacker infrastructure

➡️ Compromised host identified as:

10.2.28.88

🚨 Indicators of Compromise (IoCs)

* Malicious IP: `45.131.214.85`
* Suspicious endpoint: `/fakeurl.htm`
* User-Agent: `NetSupport Manager/1.3`
* Behavior:

  * HTTP POST beaconing
  * Encoded data exchange
  * Persistent outbound communication



🧠 Attack Summary

The compromised host establishes continuous outbound communication with a remote server acting as a Command and Control (C2).

The malware uses HTTP POST requests to:

* Send encoded data
* Receive commands
* Maintain persistence through beaconing

This behavior confirms the presence of a Remote Access Trojan (RAT) infection.


🛡️ Recommendations

* Isolate affected host from network
* Block malicious IP at firewall level
* Perform full forensic analysis
* Monitor for similar beaconing patterns
* Deploy IDS/IPS and SIEM correlation rules


📎 Full Report

Detailed PDF report available in this repository.
