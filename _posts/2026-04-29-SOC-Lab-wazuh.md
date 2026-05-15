---
layout: post
title: "SOC Lab: Building a Home SOC Lab with Wazuh"
date: 2026-04-29
categories: [Security Operations, SOC Lab]
tags: [SOC, Wazuh, SIEM, XDR, Sysmon, AD-Security, Virtualization]
pin: true
---

## Objective

The primary goal of this project was to architect and deploy a fully functional Security Operations Center (SOC) environment that integrates physical network telemetry with virtualized endpoint detection. This environment is designed to simulate real-world adversarial behavior and automate the incident response lifecycle using SIEM and XDR platforms within a strictly isolated environment.

## Architecture & Tech Stack

- **Hypervisor:** VMware Workstation Pro 17
- **SOC Node (Defense):** Ubuntu Server 24.04 LTS (Wazuh SIEM/XDR)
- **Identity Provider:** Windows Server 2022 (DC01: LAB.local Domain)
- **Victim Node (Attack):** Windows 11 Enterprise (WIN11-VIC01: Domain Joined)
- **Attacker Node:** Kali Linux (Adversary Simulation)
- **Security Tooling:** Sysmon (Olaf Hartong Config), Atomic Red Team
- **Perimeter Gateway:** UniFi Dream Machine Pro (UDM Pro)

## Phase 1: Dual-Homed Networking & Isolation

To mirror enterprise segmentation, I implemented a dual-homed networking strategy. This balances management access with strict air-gapping of the detonation zone.

### Network Segmentation
- **Management & Ingestion (VMnet0/Bridged):** The SOC Server (ens33) resides on the physical home LAN (**192.168.24.174**). This allows for Syslog ingestion from the UDM Pro and browser access from the host.
- **Internal Lab Wire (VMnet2/Host-Only):** All lab nodes communicate over an isolated **10.0.0.0/24** subnet. There is no gateway to the internet from this segment. The SOC Server (ens37) acts as the bridge at **10.0.0.2**.

## Phase 2: Node Provisioning & Hardening

### SOC Server (Ubuntu 24.04)
- **Hardening:** Disabled SSH service; management is restricted to the hypervisor console and HTTPS dashboard.
- **Resource Allocation:** 8GB RAM and 4 vCPUs. Static IP enforced via Netplan to prevent DHCP drift.

### Domain Controller (DC01)
- **Role:** Configured as the Primary Domain Controller and DNS server for **LAB.local** (10.0.0.10).
- **Hardening:** External DNS resolution disabled to maintain environment isolation.

### Victim Endpoint (WIN11-VIC01)
- **Deployment:** Integrated vTPM for localized VM encryption. Successfully joined to the LAB.local domain.
- **Security Override:** Utilized `DefenderControl` to permanently disable Windows Defender, ensuring simulations reflect a "post-compromise" scenario.

## Phase 3: SIEM Orchestration (Wazuh Deployment)

### Troubleshooting LVM Disk Exhaustion
The initial deployment failed due to the Ubuntu installer provisioning only 19GB of the 40GB virtual disk. I performed an online volume expansion to reclaim the space:
```bash
lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
resize2fs /dev/ubuntu-vg/ubuntu-lv
```

## Phase 4: Endpoint Integration (The "Python Bridge")

Because the lab nodes lack internet access, I implemented a "Python Bridge" strategy for deployment.

1. **Staging:** Downloaded agents and tools to the SOC Server via the bridged interface.
2. **Serving:** Hosted files on the internal 10.0.0.2 interface using a Python HTTP server.
3. **Enrollment:** Agents were hard-coded to report to **10.0.0.2** over port 1514. Verified DC01 and WIN11-VIC01 as active in the dashboard.

## Phase 5: Telemetry Enrichment (Sysmon Integration)

To achieve deep forensic visibility, I deployed **Microsoft Sysmon** to both the DC and Victim nodes using the **Olaf Hartong (Sysmon-Modular)** configuration.

- **Ingestion:** Modified the Windows agent `ossec.conf` on both nodes to monitor the `Microsoft-Windows-Sysmon/Operational` event channel.
- **Validation:** Confirmed Event ID 1 (Process Creation) and Event ID 3 (Network Connection) are successfully populating in the Wazuh 'Discover' tab.

## Phase 6: Adversary Simulation & Attack Surface

### Kali Linux Attacker Node
- **Networking:** Assigned static IP **10.0.0.50** on the internal segment.
- **Role:** Acts as the primary platform for manual exploitation and network pivoting.

### Automation
- **Framework:** Deployed **Invoke-AtomicRedTeam** to the Victim node.
- **Current State:** The environment is staged for its first simulation (**T1003.001: LSASS Memory Dumping**).

## Summary

This architecture provides a high-fidelity environment for analyzing modern cyber threats. By isolating the detonation zone and bridging it with a dual-homed SOC server, I have created a safe yet functional platform for complex AD-security research and detection engineering.
