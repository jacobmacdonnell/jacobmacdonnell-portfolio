---
layout: post
title: "SOC Investigation: C2 Beacon Scope Expansion (Lab Simulation)"
date: 2026-02-24
categories: [Security Operations, Incident Response]
tags: [SOC Lab, Zeek, RITA, C2, Threat Hunting]
pin: true
---

## Alert Summary

Initial investigation began with one suspected infected endpoint in a ransomware simulation.

## Evidence Observed

- Repeated outbound traffic with consistent interval behavior
- Beacon cadence consistent with command-and-control heartbeat activity
- Additional internal systems showing matching network patterns

## Investigation Steps

1. Reviewed network telemetry for periodic outbound sessions.
2. Used RITA and Zeek outputs to identify low-jitter beacon behavior.
3. Correlated host-level indicators to validate related activity.
4. Expanded scope query across internal hosts for the same pattern.
5. Documented affected systems, timeline, and escalation rationale.

## Assessment

Traffic behavior aligned with automated C2 beaconing rather than normal user activity.
Case scope expanded from one host to four based on matching heartbeat behavior.

## Scope Considerations

- Confirm affected user accounts and authentication context
- Validate persistence indicators on each scoped host
- Hunt for additional beacon variants in adjacent time windows

## Recommended Actions

- Isolate all scoped hosts
- Block confirmed malicious destinations
- Reset impacted credentials
- Continue threat hunt for related indicators across environment
