---
layout: post
title: "Project: Building a Home SOC Lab with Wazuh"
date: 2026-04-29
categories: [Security Operations, Lab Builds]
tags: [SOC, Wazuh, SIEM, XDR, Sysmon, Home Lab, Virtualization]
pin: true
---

## Objective

The primary goal of this project was to architect and deploy a fully functional Security Operations Center (SOC) environment that integrates physical network telemetry with virtualized endpoint detection. This environment is designed to simulate real-world adversarial behavior and provide a platform for high-fidelity detection engineering.

## Architecture & Tech Stack

- **Hypervisor:** VMware Workstation Pro 17
- **SOC Node (Defense):** Ubuntu Server 24.04 LTS (Wazuh SIEM/XDR)
- **Victim Node (Attack):** Windows 11 Enterprise (Sysmon + Atomic Red Team)
- **Perimeter Gateway:** UniFi Dream Machine Pro (UDM Pro)
- **Host Hardware:** Windows 11 Workstation

## Phase 1: Virtual Networking & Infrastructure

To mirror enterprise segmentation on a single workstation, I implemented a dual-homed networking strategy to balance log ingestion with malware isolation.

### Network Segmentation Strategy
- **Management & Ingestion (Bridged):** The Ubuntu SOC Server resides on the physical home LAN via a bridged adapter. This allows it to receive Syslog data directly from the physical gateway (UDM Pro) and provides a direct management interface.
- **Detonation & Isolation (NAT):** The Windows 11 Victim machine is isolated within a NAT segment. This creates a logical buffer between the detonation environment and the host OS/Home LAN, while still allowing outbound HTTPS traffic for C2 simulation and tool updates.

### Troubleshooting: IP Persistence
During setup, VMware's default "Automatic" bridging caused IP assignment failures. I remediated this by hard-coding the VMnet0 virtual switch to the physical host network adapter and implementing a strict YAML Netplan configuration within Ubuntu to enforce a static IP (**192.168.24.174**) and disable DHCP drift.

## Phase 2: Node Provisioning & Hardening

### SOC Server (Ubuntu 24.04)
- **Hardening:** Disabled the OpenSSH service to follow the Principle of Least Privilege. Management is restricted strictly to the hypervisor physical console and the HTTPS web dashboard.
- **Resource Allocation:** 8GB RAM and 4 vCPUs to ensure stable Indexer and Dashboard performance.

### Victim Endpoint (Windows 11)
- **Virtual TPM:** Integrated vTPM (Virtual Trusted Platform Module) to meet Windows 11 hardware requirements via localized VM encryption.
- **Provisioning:** Utilized `OOBE\BYPASSNRO` to bypass Microsoft Account requirements, ensuring consistent forensic testing with a local account.
- **Privacy Hardening:** Disabled all diagnostic and telemetry toggles during setup to reduce non-essential background log noise, ensuring high-fidelity alerting in the SIEM.

## Phase 3: SIEM Orchestration (Wazuh Deployment)

### Troubleshooting LVM Disk Exhaustion
The initial automated deployment failed due to the Ubuntu installer default-provisioning only 19GB of the allocated 40GB virtual disk to the Logical Volume (LVM). 

**Remediation:**
I performed an online volume expansion to reclaim the unallocated physical space:
```bash
# Grow the Logical Volume
lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv

# Expand the ext4 filesystem
resize2fs /dev/ubuntu-vg/ubuntu-lv
```
This allowed the Wazuh Indexer and Dashboard to install successfully.

## Phase 4: Endpoint Integration (The "Agent Marriage")

To begin telemetry ingestion, the Windows 11 Victim node was enrolled as an active agent within the SIEM environment.

- **Agent Deployment:** Utilized the Wazuh Dashboard's deployment wizard to generate a PowerShell-based installation string.
- **Connectivity:** Directed the agent to the static SOC Manager IP (**192.168.24.174**) over port 1514 using **AES-encrypted communication**.
- **Validation:** Verified the `WazuhSvc` is in a "Running" state on the endpoint, ensuring persistent log forwarding.

## Phase 5: Telemetry Enrichment (Sysmon Integration)

Standard Windows Event Logs are often insufficient for deep forensic analysis. To achieve visibility into process creation and network connections, I deployed **Microsoft Sysmon** to the Victim node.

- **Configuration:** Utilized the **Olaf Hartong (Sysmon-Modular)** configuration, which is mapped to the MITRE ATT&CK framework and filters out common background noise.
- **Ingestion:** Modified the Wazuh agent `ossec.conf` to monitor the `Microsoft-Windows-Sysmon/Operational` event channel.

### Validation
I verified the integration by executing `whoami` and `ipconfig` on the victim node and confirming the generation of **Event ID 1 (Process Creation)** within the Wazuh 'Discover' interface.

## Phase 6: Adversarial Simulation Readiness

To validate detection capabilities, I prepared the endpoint for automated adversarial execution.

- **Security Override:** Utilized `DefenderControl` to permanently disable Windows Defender, ensuring simulations reflect a "post-compromise" scenario.
- **Framework:** Deployed the **Invoke-AtomicRedTeam** execution framework via PowerShell (`Install-AtomicRedTeam -GetAtomics`).
- **Current State:** The environment is fully staged and awaiting execution of the initial LSASS credential dumping simulation (**T1003.001**).

## Summary

This lab build provides a robust foundation for detection engineering and security analysis. By combining Wazuh's SIEM/XDR capabilities with high-fidelity telemetry from Sysmon and automated adversarial simulation via Atomic Red Team, I have created a realistic environment for analyzing modern cyber threats.
