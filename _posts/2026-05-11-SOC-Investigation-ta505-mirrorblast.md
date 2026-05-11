---
layout: post
title: "SOC Investigation: TA505 MirrorBlast Campaign"
date: 2026-05-11
categories: [Security Operations, Incident Response]
tags: [TryHackMe, TA505, MirrorBlast, Wireshark, C2, Incident Report]
---

## Scenario

**Platform:** TryHackMe (Room: [Warzone 1](https://tryhackme.com/room/warzone1))  
**Role:** Tier 1 SOC Analyst  
**Objective:** Triage a high-priority IDS alert by analyzing a provided packet capture (`Zone1.pcap`) to confirm Command and Control (C2) activity and identify malicious artifacts.

---

## Executive Summary

Analysis of the provided packet capture (`Zone1.pcap`) confirmed the compromise of an internal endpoint by the financially motivated threat group **[TA505](https://attack.mitre.org/groups/G0092/)**. The investigation revealed a sophisticated defense evasion strategy utilizing malicious Windows Installer packages (`.msi`) to deploy the **MirrorBlast** malware family.

The core of the attack chain involved **Proxy Execution**: the adversary bundled a legitimate, signed interpreter (REBOL) inside standard installers to execute malicious script payloads. This technique allowed the threat actor to hide in plain sight, mimicking legitimate Google software directories and staying under the radar of signature-based detection.

---

## 1. Initial Alert Triage

The investigation began with a high-severity alert from the network intrusion detection system (IDS).

*   **Alert Signature:** `ET MALWARE MirrorBlast CnC Activity M3`
*   **Compromised Host:** `172.16.1.102`
*   **C2 Infrastructure:** `169.239.128.11`
*   **Vector:** TCP Port 80 (HTTP)

---

## 2. Analytical Findings

### Staging and Evasion
The adversary utilized a multi-stage download process to pull down the MirrorBlast loader. The compromised endpoint communicated with two staging servers to download distinct installer files:
1.  `filter.msi` (hosted on `185.10.68.235`)
2.  `10opd3r_load.msi` (hosted on `192.36.27.92`)

### The REBOL Abuse Angle
The most significant finding was the use of the legitimate **REBOL View 2.7.8.3.1** interpreter. Packet analysis revealed an anomalous HTTP User-Agent string originating from the host, confirming that this non-standard software engine was being used to stage further payloads.

### Host Masquerading
By extracting the HTTP streams, I identified the target directories for the dropped payloads. The adversary used a masquerading technique to blend in with legitimate system files:
*   **Path 1:** `C:\ProgramData\001\` (containing `arab.exe`)
*   **Path 2:** `C:\ProgramData\Local\Google\` (containing the REBOL interpreter and the malicious `exemple.rb` script)

**Execution Logic:** The installer silent-calls the REBOL engine to run the script:
`rebol-view-278-3-1.exe -w -i -s C:/ProgramData/Local/Google/exemple.rb`

---

## 3. Indicators of Compromise (IoCs)

### Network
*   **C2 Node:** `169.239.128.11` (fidufagios[.]com)
*   **Staging:** `185.10.68.235`, `192.36.27.92`
*   **User-Agent:** `REBOL View 2.7.8.3.1`

### Host
*   `C:\ProgramData\001\arab.exe`
*   `C:\ProgramData\Local\Google\rebol-view-278-3-1.exe`
*   `C:\ProgramData\Local\Google\exemple.rb`

---

## 4. MITRE ATT&CK Mapping

*   **[T1204.002 (User Execution: Malicious File)](https://attack.mitre.org/techniques/T1204/002/):** Initial infection via user-executed `.msi`.
*   **[T1036.005 (Masquerading: Match Legitimate Name or Location)](https://attack.mitre.org/techniques/T1036/005/):** Use of Google-themed directories to evade manual inspection.
*   **[T1129 (Shared Modules)](https://attack.mitre.org/techniques/T1129/):** Proxy execution via a signed, legitimate interpreter.
*   **[T1071.001 (Application Layer Protocol: Web Protocols)](https://attack.mitre.org/techniques/T1071/001/):** Unencrypted C2 communication over HTTP.

---

## 5. Impact & Recommendations

This investigation confirmed a successful **True Positive** compromise by TA505.

**Recommended Actions:**
1.  **Immediate Containment:** Isolate host `172.16.1.102` and block all identified IoCs at the network perimeter.
2.  **Remediation:** Purge all identified artifacts in `ProgramData`.
3.  **Detection Engineering:** Develop hunts for non-standard interpreters (REBOL, AutoIt, etc.) communicating externally or executing from user-writable directories.
