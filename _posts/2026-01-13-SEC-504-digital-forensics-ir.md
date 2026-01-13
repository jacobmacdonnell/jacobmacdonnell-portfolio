---
layout: post
title: "SEC-504: Digital Forensics & Incident Response"
date: 2026-01-13
categories: [Blue Team, DFIR]
tags: [Forensics, PowerShell, Volatility, Hayabusa, Threat Hunting, RITA, Malware Analysis]
---

In the SANS SEC504 course, I conducted a full-scope investigation into a simulated ransomware attack by the "Midnite Meerkats" threat group. This series of labs required pivoting from live system analysis to network forensics, memory forensics, malware analysis, and finally threat hunting.

Here is the complete step-by-step methodology I used to uncover the attack.

---

## 1. Live Windows Forensics (PowerShell)
**Objective:** Identify active malware on a compromised Windows 10 host using "Living off the Land" techniques.

I started with a live system that was behaving suspiciously. Instead of using third-party tools, I used administrative PowerShell to triage the host.

### Step 1: Process Enumeration
I listed all running processes to check for anomalies.
```powershell
Get-Process | Select-Object Name, Id, Path
```
I filtered for processes running from temporary directories, which is a common malware technique.
```powershell
Get-Process | Select-Object Path, Name, Id | Where-Object -Property Path -Like "*temp*"
```
**Finding:** I identified a process named `calcache` running from `C:\Users\Sec504\AppData\Local\Temp\`.

### Step 2: Network Correlation
I needed to see if this process was communicating with the network. I checked the TCP connection table.
```powershell
Get-NetTCPConnection | Select-Object LocalAddress, LocalPort, State, OwningProcess
```
**Finding:** The `calcache` process (PID 6896) was listening on port **4444** on all interfaces (`0.0.0.0`). This is a signature port for Metasploit payloads.

### Step 3: Persistence Analysis
Malware often modifies the Registry to survive reboots. I checked the standard "Run" keys.
```powershell
Get-ItemProperty "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"
```
**Finding:** I found a registry value named "Calcache" pointing to `C:\Users\Sec504\AppData\Local\Temp\calcache.exe`, confirming the malware had established persistence.

---

## 2. Network Forensics (PCAP Analysis)
**Objective:** Pivot from the endpoint to the network to scope the compromise.

After identifying the compromised host, I analyzed packet captures (`.pcap`) and proxy logs to identify other infected systems.

### Step 1: Identifying the C2 Domain
I examined the proxy logs (`access.log`) for connections initiated by the compromised host. I looked for high-frequency connections or suspicious domains.
```bash
awk '{print $1, $7}' access.log | sort | uniq -c | sort -n
```
**Finding:** I found repeated connections to `www1-google-analytics.com`. Note the "1" in "www1"—a typo-squatting domain designed to look like legitimate Google traffic.

### Step 2: Scoping the Incident
I searched the logs for *any* other internal IP address connecting to this malicious domain.
```bash
grep "www1-google-analytics.com" access.log | awk '{print $3}' | sort -u
```
**Finding:** This query returned **three additional internal IP addresses** (172.16.42.105, .107, .108) that were also communicating with the C2 server. This allowed me to instantly update the incident scope from 1 host to 4 hosts.

---

## 3. Memory Forensics (Volatility)
**Objective:** Recover volatile evidence that never touches the disk.

Advanced malware often resides solely in RAM. I took a memory image (`FM-TETRIS.mem`) from one of the infected hosts and analyzed it using **Volatility 3**.

### Step 1: Process Tree Analysis
I generated a process tree to visualize parent-child relationships.
```bash
vol -f FM-TETRIS.mem windows.pstree.PsTree
```
**Finding:** I observed the following execution chain:
`ONENOTE.EXE` -> `cmd.exe` -> `powershell.exe` -> `analytics.exe`
This pattern confirms the initial infection vector was likely a malicious OneNote document that executed a command to spawn PowerShell, which then downloaded the payload.

### Step 2: Retrieving Network Artifacts from RAM
I recovered network connections that were active at the time of the memory dump.
```bash
vol -f FM-TETRIS.mem windows.netscan.NetScan | grep "ESTABLISHED"
```
**Finding:** I saw an `ESTABLISHED` connection to `167.172.201.123` on port 80, confirming the C2 IP address.

---

## 4. Malware Investigation
**Objective:** Reverse engineer the dropper to understand its capabilities.

I recovered the installer binary `AnalyticsInstaller.exe` and performed static and dynamic analysis.

### Step 1: Static Analysis (Strings & Hashes)
I calculated the file hash to check against threat intelligence feeds (VirusTotal).
```powershell
Get-FileHash -Algorithm SHA256 AnalyticsInstaller.exe
```
I ran `strings` to look for hardcoded domains or paths.
```bash
strings -n 10 AnalyticsInstaller.exe
```
**Finding:** I found the string `cmd.exe /c rd c:\ /s /q`. This command recursively deletes the `C:\` drive—this is a **wiper**, not just ransomware.

### Step 2: Dynamic Analysis (Regshot & Procmon)
I used **Regshot** to take "before" and "after" snapshots of the registry.
*Result:* It revealed the malware created a Scheduled Task named "Analytics Backup" to ensure persistence.

I used **Procmon** (Process Monitor) to watch the malware in real-time.
*Filter:* `Process Name` is `AnalyticsInstaller.exe`.
*Result:* I saw the malware creating the batch file `AnalyticsBackup.bat` which contained the wipe command.

---

## 5. Log Analysis (Hayabusa & Sigma)
**Objective:** Scale the investigation by analyzing Windows Event Logs across the enterprise.

Manually reading Event Viewer is impossible at scale. I used **Hayabusa**, a high-speed forensic timeline generator that uses **Sigma** rules to detect attack patterns.

### Step 1: Automated Threat Detection
I ran Hayabusa against a folder of `.evtx` files exported from the domain controller.
```powershell
.\hayabusa.exe csv-timeline --directory C:\Tools\win10evtx\ -o win10-threatdetect.csv
```

### Step 2: Timeline Analysis
I opened the resulting CSV in Timeline Explorer and filtered for "High" and "Critical" alerts.
**Findings:**
*   **Event ID 7045 (Service Installation):** A service named "HealthService" was installed but pointed to a binary in the `Temp` folder.
*   **Event ID 4624 (Logon):** I tracked the attacker's lateral movement by observing Type 3 (Network) logons using the compromised "tdoudney" account.

---

## 6. Threat Hunting (RITA & Zeek)
**Objective:** Proactively hunt for "low and slow" C2 beacons that evade firewall rules.

Finally, I used **RITA (Real Intelligence Threat Analytics)** to analyze **Zeek** network logs. Unlike standard firewalls that look for known bad IPs, RITA looks for *mathematical patterns* in connection timing.

### Step 1: Processing Logs
I converted the PCAP to Zeek logs (`conn.log`, `dns.log`, etc.) and imported them into the RITA database.
```bash
zeek -Cr netedge.pcap
rita import . netedge-dataset
```

### Step 2: Beacon Analysis
I queried the dataset for connections with low "jitter" (variance).
```bash
rita show-beacons netedge-dataset
```
**Finding:** RITA identified a connection to an external IP that occurred exactly every **60 seconds** with almost zero variance. Humans don't browse the web with that kind of precision—automated beacons do. This behavior is a strong indicator of a C2 heartbeat.

```