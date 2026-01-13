---
layout: post
title: "SEC-504: Password Attacks & Credential Harvesting"
date: 2026-01-13
categories: [Red Team, Password Cracking]
tags: [Hashcat, Responder, Password Spraying, Legba, Active Directory]
---

In the SANS SEC504 course, I explored the techniques used to capture and crack credentials. Credentials are the keys to the kingdom; acquiring them allows an attacker to bypass sophisticated exploits and simply "log in."

Here is the step-by-step methodology I used.

---

## 1. Online Password Attacks (Legba)
**Objective:** Guess credentials against live services (HTTP, MySQL, SMB).

I used **Legba**, a high-performance protocol brute-forcer, to attack services on the target network.

### Step 1: Dictionary Attack against HTTP
I targeted an embedded device with a list of default credentials.
```bash
legba -C credentials.txt -T 172.30.0.12 http.basic
```
**Result:** Found valid credentials `admin:tiksight`.

### Step 2: Credential Reuse
I tried these same credentials against the MySQL server (`172.30.0.64`). They failed, so I switched to a standard brute-force against the `root` account.
```bash
legba -U root -P 10k-most-common.txt -T 172.30.0.64 mysql
```
**Result:** Found password `changeme`.

---

## 2. Password Spraying
**Objective:** Test cloud identity security without locking out accounts.

I audited a Microsoft 365 tenant using a "Password Spray" technique. Unlike a brute-force attack (many passwords vs one user), I tried **one password vs many users**.

### The Methodology
1.  **Target List:** I compiled a list of usernames (`falsimentisusernames.txt`).
2.  **The Password:** I selected "Falsimentis123" (a likely weak password).
3.  **The Spray:** I used Legba to send a single login attempt for each user against the SMB service.
    ```bash
    legba -U falsimentisusernames.txt -P Falsimentis123 -T 172.30.0.155 smb
    ```
4.  **The Result:** I successfully compromised multiple accounts.

**Impact:** Because I only tried one password per account, I did **not** trigger the "5 failed attempts" account lockout policy, allowing the attack to go unnoticed.

---

## 3. Password Cracking with Hashcat
**Objective:** Recover cleartext passwords from captured hashes.

I extracted password hashes from a Linux system (`/etc/shadow`) and an Active Directory database (`ntds.dit`).

### Step 1: Identifying Hash Types
I used Hashcat to automatically identify the hash type.
```bash
hashcat hashes.txt --identify
```
**Result:** Identified `md5crypt` (Mode 500) and `NTLM` (Mode 1000).

### Step 2: Dictionary Attack
I ran a straight dictionary attack using the `rockyou.txt` wordlist.
```bash
hashcat -m 500 hashes.txt /usr/share/wordlists/rockyou.txt
```
**Result:** This cracked simple passwords like "basketball" instantly.

### Step 3: Rule-Based Attack
To crack more complex passwords (e.g., "Password123"), I applied **rules** to the wordlist. This creates mutations (adding numbers, capitalizing letters) of the dictionary words.
```bash
hashcat -m 1000 domain_hashes.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```
**Result:** This successfully cracked passwords that followed corporate complexity policies (e.g., "Summer2025!").

---

## 4. Network Credential Harvesting (Responder)
**Objective:** Steal authentication hashes from the local network.

I used **Responder**, a tool that poisons network protocols like LLMNR and NBT-NS.

### Step 1: Starting Responder
I started Responder on my listening interface.
```bash
sudo responder -I eth0 -w
```
*   `-I eth0`: Interface to listen on.
*   `-w`: Start the WPAD rogue proxy server.

### Step 2: The Attack
I simulated a user mistyping a file share name (typing `\\prntserver` instead of `\\printserver`). Windows broadcasted a query to the local network asking, "Who is prntserver?"

### Step 3: The Capture
Responder answered, "I am prntserver!" The victim's computer then attempted to authenticate to my machine, sending its **NetNTLMv2** password hash.

### Step 4: Cracking the Hash
I took the captured hash and cracked it using Hashcat (Mode 5600) to retrieve the user's plaintext password.
```bash
hashcat -m 5600 captured_hash.txt rockyou.txt
```
