[![Netlify Status](https://api.netlify.com/api/v1/badges/7922f4d1-efe7-4cc4-9a93-d75b1fffc118/deploy-status)](https://jacobmacdonnell.com)
# Jacob Macdonnell - Security Operations Portfolio

This portfolio presents applied Security Operations work through a modern SOC analyst workbench design. It documents alert triage, Windows Event Log analysis, incident response workflows, SOC lab builds, and hands-on defensive analysis.

Primary Focus:
- Tier 1 SOC alert triage
- Windows Event Log analysis
- Escalation documentation
- Investigation scoping
- Incident response fundamentals

This site reflects practical, operational work, not generic coursework summaries.

View live: https://jacobmacdonnell.com

## Site Structure

- `index.html` uses the custom `_layouts/home.html` portfolio dashboard.
- `_layouts/home.html` renders the homepage hero, stats, featured case files, focus areas, tooling stack, and recent writeups.
- `assets/css/portfolio.css` contains the scoped visual layer for the custom homepage.
- `_posts/` contains all case files/writeups.
- `_tabs/about.md` contains the analyst profile.
- `DESIGN.md` defines the visual design system.
- `AGENTS.md` defines implementation rules for future coding agents.
- `_redirects` preserves renamed post URLs on Netlify.

## Design Direction

The current design direction is a polished SOC analyst workbench:

- dark graphite surfaces
- restrained cyan, green, and amber accents
- case-file style post cards
- compact telemetry/tag chips
- evidence-oriented copy
- no gimmicky hacker visuals or excessive neon

Before changing homepage layout, colors, cards, or interaction details, read `DESIGN.md`.

## Local Development

Install Ruby and Bundler, then run:

```powershell
bundle install
bundle exec jekyll serve
```

Useful static checks:

```powershell
rg -n "^(layout|title|date|categories|tags|pin):" _posts
rg -n "Google-Cyber-|PicoCTF|THM|EventID|Pentesting|Home Lab|categories: \[(Blue Team|Red Team|CTF Competitions)|CTF Challenges" _posts _tabs _config.yml
git diff --check
```

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
pin: true
---
```

`pin` is optional. Use it only for homepage-featured work.

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

## URL And Redirect Rules

Post filenames determine generated URLs through the `_config.yml` permalink setting:

```yaml
permalink: /posts/:title/
```

If a published filename changes, add a lowercase redirect in `_redirects`.

Example:

```text
/posts/old-post-slug/ /posts/new-post-slug/ 301
```
