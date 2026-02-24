[![Netlify Status](https://api.netlify.com/api/v1/badges/7922f4d1-efe7-4cc4-9a93-d75b1fffc118/deploy-status)](https://jacobmacdonnell.com)
# Jacob Macdonnell – Security Operations Portfolio

This portfolio documents applied Security Operations investigations, incident triage workflows, and hands-on defensive analysis.

Primary Focus:
- Tier 1 SOC alert triage
- Windows Event Log analysis
- Escalation documentation
- Investigation scoping
- Incident response fundamentals

This site reflects practical, operational work — not coursework summaries.

👉 View live: https://jacobmacdonnell.com

## How to Add New Posts

To maintain consistency, all new writeups must follow these strict standards.

### 1. File Naming Convention
Files must be placed in `_posts/` and follow this format:
`YYYY-MM-DD-Source-topic-name.md`

*   **Source:** Capitalized (e.g., `GCIH`, `Google-Cybersecurity`, `PicoCTF`, `TryHackMe`, `NCL`).
*   **Topic:** Lowercase, kebab-case (e.g., `linux-file-permissions`, `advanced-exploitation`).

**Examples:**
*   `2026-01-13-GCIH-advanced-exploitation.md`
*   `2024-04-01-Google-Cybersecurity-linux-file-permissions.md`
*   `2025-03-05-PicoCTF-big-zip.md`

### 2. Front Matter Template
Every post must start with this YAML header:

```yaml
---
layout: post
title: "Source: Topic Name"
date: YYYY-MM-DD
categories: [Functional Domain, Activity]
tags: [Source, Tool1, Tool2, Concept]
---
```

**Title Format:**
*   Use an operator-focused title that reflects the artifact type.
*   Investigation example: `title: "SOC Investigation: Suspicious PowerShell Execution"`
*   Playbook example: `title: "SOC Playbook: Suspicious PowerShell Triage"`
*   Source-style lab example: `title: "SEC-504: Digital Forensics & Incident Response"`

**Categories:**
Choose **one** primary functional domain:
*   `[Red Team, Exploitation]`
*   `[Red Team, Reconnaissance]`
*   `[Red Team, Password Cracking]`
*   `[Red Team, Cloud Security]`
*   `[Red Team, Web Security]`
*   `[Blue Team, DFIR]`
*   `[Blue Team, Threat Hunting]`
*   `[Blue Team, Security Automation]`
*   `[Blue Team, System Hardening]`
*   `[Security Operations, Alert Triage]`
*   `[Security Operations, Incident Investigation]`
*   `[CTF Competitions, Forensics]`
*   `[CTF Competitions, Events]`
*   `[CTF Competitions, Walkthroughs]`

**Tags:**
*   Always include the **Source** as a tag (e.g., `GCIH`, `Google Cybersecurity`, `picoCTF`).
*   Include specific tools and concepts (e.g., `Nmap`, `Wireshark`, `Linux`, `SQL`).
