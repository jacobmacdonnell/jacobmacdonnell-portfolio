---
layout: post
title: "SOC Playbook: Suspicious PowerShell Triage"
date: 2026-02-24
categories: [Security Operations, Alert Triage]
tags: [SOC, Playbook, PowerShell, Windows, Event ID 4688]
pin: true
---

## Purpose

Standardize Tier 1 triage for suspicious PowerShell alerts in lab and simulation workflows.

## Required Data

- EDR or endpoint process telemetry
- Windows Security logs (Event ID 4688)
- Authentication logs (Event ID 4624)
- Service creation logs (Event ID 7045)
- Network telemetry (proxy, Zeek, or firewall)

## Initial Triage Checklist

1. Confirm host, user, timestamp, and detection source.
2. Validate whether PowerShell execution is expected for the user role.
3. Review full command-line arguments for encoded or obfuscated content.
4. Identify parent process and execution lineage.
5. Correlate with outbound network activity from the same host and time window.

## Escalation Triggers

Escalate to Incident Investigation when one or more of the following are present:

- Encoded command arguments
- Office application launching PowerShell
- New suspicious service installation (7045)
- Repeated failed/suspicious network logon patterns (4624 context)
- External beacon-like network behavior

## Containment Guidance

- Isolate affected host if malicious behavior is confirmed or highly likely.
- Block confirmed malicious IPs/domains.
- Reset affected user credentials when credential abuse is suspected.
- Initiate full endpoint scan and hunt for related indicators.

## Documentation Standard

Every case update should capture:

- Alert source and severity
- What was observed (facts only)
- What was ruled out
- Scope status (single host vs multi-host)
- Escalation decision and rationale
- Containment and follow-up actions
