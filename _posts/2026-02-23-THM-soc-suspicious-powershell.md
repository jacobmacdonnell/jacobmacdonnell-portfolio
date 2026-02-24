---
layout: post
title: "SOC Investigation: Suspicious PowerShell Execution"
date: 2026-02-23
categories: [Security Operations, Alert Triage]
pin: true
tags: [SOC, Windows, EventID 4688, PowerShell, Investigation]
---

## Alert Summary

Detection triggered for suspicious PowerShell execution on host WIN-01 under user jsmith.

## Evidence Observed

- Event ID 4688 – Process creation
- Command line included base64-encoded payload
- Parent process: winword.exe
- Network connection to external IP 185.x.x.x

## Investigation Steps

1. Decoded PowerShell payload
2. Checked process tree lineage
3. Reviewed additional 4688 events for lateral movement
4. Queried for matching hashes across environment

## Assessment

Execution pattern consistent with macro-based initial access.
Behavior unlikely to be benign due to:
- Encoded command
- Office parent process
- External C2 beacon attempt

## Scope Considerations

- Check other hosts for same hash
- Review authentication logs (4624) for lateral movement
- Monitor for new service creation (7045)

## Recommended Actions

- Isolate affected host
- Reset user credentials
- Block external IP
- Initiate full EDR scan
