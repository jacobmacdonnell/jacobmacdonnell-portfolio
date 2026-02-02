[![Netlify Status](https://api.netlify.com/api/v1/badges/7922f4d1-efe7-4cc4-9a93-d75b1fffc118/deploy-status)](https://jacobmacdonnell.com)
# Jacob Macdonnell's Cybersecurity Portfolio 

Welcome to my professional cybersecurity portfolio.  
Explore detailed breakdowns of my projects, CTFs, and more!

👉 **View here:** [jacobmacdonnell.com](https://jacobmacdonnell.com)

## 📝 How to Add New Posts

To maintain consistency, all new write-ups must follow these strict standards.

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
*   Always use `Source: Topic` format.
*   *Example:* `title: "GCIH: Web Application Pentesting"`

**Categories:**
Choose **one** primary functional domain:
*   `[Red Team, Exploitation]`
*   `[Red Team, Reconnaissance]`
*   `[Blue Team, DFIR]`
*   `[Blue Team, Threat Hunting]`
*   `[CTF Competitions, Forensics]`
*   `[CTF Competitions, Walkthroughs]`

**Tags:**
*   Always include the **Source** as a tag (e.g., `GCIH`, `Google Cybersecurity`, `picoCTF`).
*   Include specific tools and concepts (e.g., `Nmap`, `Wireshark`, `Linux`, `SQL`).
