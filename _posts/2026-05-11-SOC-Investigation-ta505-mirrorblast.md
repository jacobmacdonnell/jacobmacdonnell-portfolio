---
layout: post
title: "SOC Investigation: TA505 MirrorBlast Campaign"
date: 2026-05-11
categories: [Security Operations, Incident Response]
tags: [TryHackMe, TA505, MirrorBlast, Wireshark, C2, Incident Report]
---

## Executive Summary

Analysis of the provided packet capture (`Zone1.pcap`) confirmed the compromise of an internal endpoint by the financially motivated threat group **TA505**. The adversary utilized malicious Windows Installer packages (`.msi`) to deploy the **MirrorBlast** malware family.

The attack chain demonstrated distinct defense evasion tradecraft: bundling a legitimate, lightweight interpreter (REBOL) inside standard installers to execute script payloads from directory paths designed to mimic legitimate Google software.

---

## 1. Initial Alert Triage

The investigation began by parsing network intrusion detection system (IDS) logs using Brim (Suricata integration), identifying a high-severity signature match.

* **Alert Signature:** `ET MALWARE MirrorBlast CnC Activity M3`
* **Compromised Internal Host:** `172.16.1.102`
* **External C2 Node:** `169.239.128.11`
* **Transport Protocol:** TCP (Port 80 / HTTP)

---

## 2. Threat Intelligence & Attribution

Pivoting the destination IP (`169.239.128.11`) into VirusTotal provided immediate behavioral context and historical infrastructure mapping.

* **Associated Domain:** `fidufagios[.]com`
* **Threat Group Attribution:** TA505
* **Associated Malware Family:** MirrorBlast
* **Primary Delivery Mechanism:** Malicious `.msi` (Windows Installer) attachments typically distributed via phishing campaigns.

---

## 3. Traffic Analysis & Payload Staging

Filtering HTTP traffic in Wireshark (`http.request.method == GET`) revealed the staging infrastructure used to pull down the initial payloads. The compromised endpoint communicated with two secondary malicious infrastructure IPs to download distinct installer files.

### Identified File Downloads

1. **Payload 1:** `filter.msi` hosted on `185.10.68.235`
2. **Payload 2:** `10opd3r_load.msi` hosted on `192.36.27.92`

### Anomalous Network Artifacts

During the download phases, packet analysis revealed an unusual HTTP User-Agent string originating from the internal host:

```text
User-Agent: REBOL View 2.7.8.3.1
```

This indicated that a non-standard software execution engine was actively making web requests to stage further payloads.

---

## 4. Host Artifact Identification & Payload Deep-Dive

To determine the host-level impact without direct disk access, manual stream extraction was performed on the HTTP traffic delivering the `.msi` files. Parsing the unencrypted strings within the installation binaries exposed the target extraction directories and dropped file names.

### Staging Payload 1 (`filter.msi`)

Following the TCP stream for the delivery of `filter.msi` revealed that the package drops executable binaries directly into a newly created numerical directory inside `ProgramData`.

* **Dropped File 1:** `C:\ProgramData\001\arab.bin`
* **Dropped File 2:** `C:\ProgramData\001\arab.exe`

### Staging Payload 2 (`10opd3r_load.msi`)

Stream analysis of the second installer revealed a sophisticated masquerading technique. The installer constructs a fake directory path mimicking Google software installations to hide the embedded REBOL interpreter and its execution script.

* **Interpreter Path:** `C:\ProgramData\Local\Google\rebol-view-278-3-1.exe`
* **Script Payload Path:** `C:\ProgramData\Local\Google\exemple.rb`

**Execution Logic:** The installer extracts the REBOL executable to silently call the dropped `.rb` script using command-line switches:

```cmd
rebol-view-278-3-1.exe -w -i -s C:/ProgramData/Local/Google/exemple.rb
```

---

## 5. Indicators of Compromise (IoCs)

### Network Indicators

| Type | Indicator | Context |
| --- | --- | --- |
| **IPv4** | `169.239.128.11` | Primary MirrorBlast C2 Node |
| **IPv4** | `185.10.68.235` | Staging Server (`filter.msi`) |
| **IPv4** | `192.36.27.92` | Staging Server (`10opd3r_load.msi`) |
| **Domain** | `fidufagios[.]com` | Resolved C2 Infrastructure |
| **User-Agent** | `REBOL View 2.7.8.3.1` | Anomalous client request string |

### Host Indicators (File Paths)

```text
C:\ProgramData\001\arab.bin
C:\ProgramData\001\arab.exe
C:\ProgramData\Local\Google\rebol-view-278-3-1.exe
C:\ProgramData\Local\Google\exemple.rb
```

---

## 6. Adversary Tradecraft Summary (MITRE ATT&CK)

* **T1204.002 (User Execution: Malicious File):** Initial execution relying on a user executing a malicious `.msi` installer.
* **T1036.005 (Masquerading: Match Legitimate Name or Location):** Creation of the `C:\ProgramData\Local\Google\` directory path to evade basic manual inspection.
* **T1129 (Shared Modules):** Use of an embedded, signed or legitimate interpreter (`rebol-view`) to proxy execution of the primary payload script (`exemple.rb`).
* **T1071.001 (Application Layer Protocol: Web Protocols):** Exfiltration and command communication routed over standard unencrypted HTTP.

---

## 7. Conclusion & Containment Recommendations

The incident represents a successful deployment of TA505's MirrorBlast loader. Immediate remediation steps require:

1. **Network Containment:** Isolate host `172.16.1.102` from the corporate network immediately. Block outbound traffic to all three identified external IP addresses at the perimeter firewall.
2. **Host Remediation:** Purge all identified artifacts within `C:\ProgramData\001\` and `C:\ProgramData\Local\Google\`.
3. **Root Cause Analysis:** Pull endpoint logs (EDR/Sysmon) to identify the initial delivery vector (likely a malicious email attachment or link leading to the execution of `filter.msi`).
