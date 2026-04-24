# Multi-Stage APT Attack Detection with Security Onion

"Turning 92,000 raw events into 1 actionable timeline."
This repository documents a full-scale APT simulation and forensic investigation. By leveraging  [Security Onion](https://securityonion.net/), I successfully identified and reconstructed a multi-stage attack lifecycle—from initial phishing delivery and UAC bypass to credential harvesting and data exfiltration—using pure network-layer telemetry.

## 🌐 Lab Topology

A dedicated offensive lab was constructed on a shared virtual network, leveraging Kali Linux for the attack origin and a Windows 10 target. Security Onion was configured as the passive network sensor, monitoring the complete multi-stage traffic flow:

![Security Onion APT Lab Network Diagram](Network_Diagram.png)

## 📊 The Investigative Challenge: High-Fidelity Data Correlation

In a modern SOC environment, the challenge isn't a lack of data—it's the noise. As shown in the **Security Onion Dashboard** below, the lab environment processed over **92,000 system events** and thousands of network metadata entries during the attack window.

My objective was to filter through this massive telemetry to identify the "Signal in the Noise." By correlating **Suricata NIDS alerts** with **Zeek connection logs**, I successfully isolated the 11 critical stages of the APT lifecycle, transforming thousands of raw data points into a single, actionable forensic timeline.

![Security Onion Overview Dashboard](Alerts_Dashboard.png)
*Figure 1: Security Onion 'Overview' interface displaying the distribution of event modules (Suricata, Zeek, System) used during the investigation.*

## 🕵️ Executive Summary

This project showcases a comprehensive investigation of a multi-stage Advanced Persistent Threat (APT) attack within a controlled lab environment. Using Security Onion, I successfully monitored and analyzed a complete attack chain—from initial phishing delivery to DNS-based data exfiltration.

## 🔍 Detection Deep Dive: The APT Lifecycle

This investigation tracked the adversary from initial delivery to full domain compromise. Below are the key milestones captured during the analysis:

### Phase 1: Successful Exploit & Backdoor Establishment
The attack began with the execution of `invoice_Q1.exe`. Security Onion's Suricata engine immediately flagged the **Meterpreter Reverse TCP** check-in.
* **Evidence:** Captures showed a recursive connection to the C2 on Port 4444.
* 

### Phase 2: Privilege Escalation & Process Migration
Once a low-privilege shell was established, the attacker used `bypassuac_fodhelper`. I monitored the transition from a standard user context to `NT AUTHORITY\SYSTEM`.
* **Detection:** Observed process migration into `explorer.exe` to blend in with legitimate system traffic and maintain persistence.
* 

### Phase 3: Credential Access (LSA Secrets)
With SYSTEM-level access, the adversary executed credential harvesting. 
* **Detection:** Security Onion flagged the characteristic patterns of **Mimikatz/Kiwi** activity, specifically the dumping of LSA secrets and SAM database hashes.
*

## 📑 Key Achievement

Correlated 11 unique Suricata alerts to reconstruct a timeline of attacker activity without relying on host-based telemetry, demonstrating the power of network traffic analysis (NTA) in identifying stealthy persistence and credential dumping techniques.

## 🛡️ MITRE ATT&CK Mapping

## Attack Lifecycle (MITRE ATT&CK)

| Tactic                  | Technique                     | Evidence                                      |
|------------------------|-------------------------------|-----------------------------------------------|
| Initial Access         | Phishing (T1566.001)          | invoice_Q1.exe delivered via HTTP             |
| Privilege Escalation   | UAC Bypass (T1548.002)        | bypassuac_fodhelper execution                 |
| Credential Access      | LSASS Dumping (T1003.001)     | Mimikatz/Kiwi hashdump                        |
| Exfiltration           | DNS Tunneling (T1048.003)     | PowerShell Resolve-DnsName patterns           |
