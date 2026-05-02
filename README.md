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

To maintain consistency, all new write-ups must follow these strict standards.

### 1. File Naming Convention
Files must be placed in `_posts/` and follow this format:
`YYYY-MM-DD-Source-topic-name.md`

*   **Source:** Use the official source/platform casing (e.g., `SEC-504`, `Google-Cybersecurity`, `picoCTF`, `TryHackMe`, `NCL`).
*   **Topic:** Lowercase, kebab-case (e.g., `linux-file-permissions`, `advanced-exploitation`).

**Examples:**
*   `2026-01-13-SEC-504-advanced-exploitation.md`
*   `2024-04-01-Google-Cybersecurity-linux-file-permissions.md`
*   `2025-03-05-picoCTF-big-zip-challenge.md`

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
Choose **one** primary professional domain and one specific activity:
*   `[Security Operations, Alert Triage]`
*   `[Security Operations, Incident Response]`
*   `[Security Operations, SOC Lab]`
*   `[Security Engineering, Automation]`
*   `[Security Engineering, System Hardening]`
*   `[Offensive Security, Exploitation]`
*   `[Offensive Security, Reconnaissance]`
*   `[Offensive Security, Credential Attacks]`
*   `[Offensive Security, Cloud Security]`
*   `[Offensive Security, Web Security]`
*   `[CTF Labs, Forensics]`
*   `[CTF Labs, Competition]`
*   `[CTF Labs, Walkthrough]`

**Tags:**
*   Include the source/platform when applicable (e.g., `SEC-504`, `Google Cybersecurity`, `picoCTF`).
*   Include specific tools and concepts (e.g., `Nmap`, `Wireshark`, `Linux`, `SQL`).
*   Prefer professional names over shorthand (e.g., `Penetration Testing`, `Event ID 4688`).
*   Do not duplicate source acronyms and full names in the same post (e.g., choose `National Cyber League` or `NCL`, not both).
*   Avoid vague tags that only repeat the category, such as `Investigation` or `CTF Challenges`.
