---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

## Professional Summary

Entry-level SOC Analyst with hands-on lab and simulation experience in security monitoring, alert triage, log investigation, and incident documentation. Comfortable operating a ticket-driven alert lifecycle (acknowledge, investigate, document evidence, escalate with severity/context, close with resolution criteria), using playbooks/runbooks (SOPs) and workbooks/lookups, tracking SOC metrics (MTTD/MTTA/MTTR), and investigating ransomware/C2 using Splunk, Zeek/RITA, Windows Event Logs, Wireshark, and Volatility; mapped findings to MITRE ATT&CK and produced containment recommendations.

### Certifications
- GIAC Certified Incident Handler (GCIH) — 92%
- GIAC Security Essentials (GSEC) — 98%
- GIAC Foundational Cybersecurity Technologies (GFACT)

### Applied Cybersecurity Certificate (ACS) — *In Progress*
SANS Technology Institute

### SOC Level 1 Path (2026) — *In Progress*
TryHackMe

### Google Cybersecurity Professional Certificate — 2024
Google / Coursera

### Bachelor of Management (Honours), Minor in Psychology — 2023
University of British Columbia (UBC)

## Technical Skills
- **Security Operations:** Alert triage, event investigation, SIEM log search and correlation (Splunk SPL fundamentals), severity triage, escalation and closure criteria, ticket-based case documentation, Windows Event Log analysis, MITRE ATT&CK mapping, incident documentation procedures
- **Network and Forensics:** Network traffic analysis (PCAP, Zeek, RITA), memory analysis (Volatility 3), forensic timeline reconstruction (Hayabusa, Sigma)
- **Tools and Platforms:** Splunk, Zeek, RITA, Wireshark, Volatility 3, Hayabusa (Sigma), AWS CLI
- **Systems and Scripting Familiarity:** Windows Server (Active Directory), Linux (REMnux, Kali), AWS, GCP, Python (basic scripting, code review), PowerShell (basic scripting), SQL (basic queries)
- **AI and Documentation Tools:** Claude, Gemini, Codex

## Projects

Check out my [Security Operations posts](/categories/security-operations/) and [all categories](/categories/) for full writeups.

### Enterprise Incident Response and Threat Hunting Simulation
- Investigated simulated ransomware activity by performing live triage and analyzing memory dumps with Volatility 3; reconstructed execution chain (OneNote.exe -> cmd.exe -> powershell.exe) to identify destructive wiper behaviour and determine scope of compromise.
- Detected low-and-slow command-and-control beaconing using RITA and Zeek network timing analysis; expanded incident scope from one infected host to four through 60-second heartbeat correlation.
- Engineered forensic timelines from Windows Event Logs (.evtx) using Hayabusa and Sigma rules, correlating Service Installations (ID 7045) with Network Logons (ID 4624) to track lateral movement.
- Documented investigation steps, timeline, and escalation rationale in a ticket-style incident report (severity, scope, IOCs, and recommended containment).

### National Cyber League (Spring 2025) | Network Analysis & Team Coordination
- Ranked 521st/8,569 individually (Top 6%) and 63rd/4,798 as a team (Top 1.3% nationally) during timed investigations across reconnaissance, OSINT, and forensic analysis challenges.
- Applied Wireshark filtering to validate alerts, extract IOCs from HTTP headers and User-Agent strings, and identify web-based attack patterns.

### Independent Lab Development & CTF Scenarios | TryHackMe & PicoCTF
- Conducted Linux forensic investigations (recursive grep, archive extraction) to identify malicious artifacts.
- Analyzed privilege escalation paths and mapped attacker activity to MITRE ATT&CK.
