---
layout: post
title: "SOC Reference: Windows Log Quick Reference"
date: 2026-02-24
categories: [Security Operations, Incident Investigation]
tags: [SOC, Windows, Event Logs, EventID 4688, EventID 4624, EventID 7045, Reference]
---

## Scope

Quick-reference event IDs used regularly in SOC triage and investigation workflows.

## Event ID 4688 - Process Creation

Use to confirm process execution details:

- Process name
- Command-line arguments
- Parent process context
- User/security context

High-priority patterns:

- Encoded or obfuscated PowerShell arguments
- Office process spawning script interpreters
- Unusual process execution paths

## Event ID 4624 - Successful Logon

Use to investigate authentication behavior:

- Logon type
- Source workstation/IP
- Account used
- Time correlation with suspicious process or network events

High-priority patterns:

- Unexpected network logons tied to incident windows
- Account activity on systems outside normal scope

## Event ID 7045 - New Service Installation

Use to detect persistence and post-compromise activity:

- Service name
- Binary path
- Account context
- Install timestamp

High-priority patterns:

- Service binaries executing from temporary or user-writable paths
- Service creation near suspicious authentication or process events

## Minimal Correlation Workflow

1. Start with the triggering process (4688).
2. Correlate account access patterns (4624).
3. Check persistence indicators (7045).
4. Expand scope to additional hosts and accounts when indicators repeat.
