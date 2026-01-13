---
layout: post
title: "SEC-504: Cloud Security & AWS Exploitation"
date: 2026-01-13
categories: [Red Team, Cloud Security]
tags: [AWS, S3, SSRF, IMDS, ScoutSuite, Pentesting, Masscan]
---

In the SANS SEC504 course, I focused on the unique misconfigurations found in cloud environments, specifically AWS S3 buckets and Cloud metadata services. The goal was to understand how simple configuration errors can lead to total data exposure.

Here is the step-by-step methodology I used to exploit these cloud services.

---

## 1. Cloud Network Scanning
**Objective:** Identify cloud assets and attribute them to the target organization using certificates.

Scanning cloud ranges (like AWS IP blocks) is noisy. I used **Masscan** for speed and **TLS-Scan** for attribution.

### Step 1: High-Speed Scanning
I scanned a target IP range (`10.200.0.0/16`) for web servers on port 443 at a rate of 10,000 packets per second.
```bash
masscan -p 443 --rate 10000 -oL simcloud.txt 10.200.0.0/16
```

### Step 2: TLS Attribute Analysis
Masscan only gives IPs. To know *who* owns the servers, I used **TLS-Scan** to grab the SSL certificates.
```bash
tls-scan --port=443 --cacert=ca-bundle.crt -o tls-results.json < targets.txt
```

### Step 3: Parsing Data
I used **JQ** to parse the JSON output and filter for the organization's name ("Falsimentis").
```bash
jq '.ip + " " + .certificateChain[].subjectCN' tls-results.json | grep falsimentis
```
**Result:** This revealed a hidden download server (`downloads.falsimentis.com`) hosted in the cloud range that was not in public DNS records.

---

## 2. Cloud Bucket Discovery & Enumeration
**Objective:** Identify and access misconfigured public S3 buckets.

Cloud storage is secure by default, but human error often leaves it wide open. I simulated an attacker discovering hidden S3 buckets linked to a target organization.

### Step 1: Bucket Name Guessing
Bucket names must be globally unique DNS names. I used a wordlist generator to create permutations of the company name (e.g., `falsimentis-dev`, `falsimentis-backup`).

### Step 2: Listing Contents
I used the standard AWS CLI to interact with the buckets. I attempted to list the contents of a bucket I identified named `mybucket2`.
```bash
aws s3 ls s3://mybucket2
```
**Result:** The command listed files, confirming the bucket had `Public` read permissions. I found sensitive employee records inside.

### Step 3: Uploading Malicious Files
I tested for write permissions by attempting to upload a file.
```bash
aws s3 cp pslist.txt s3://mybucket2/
```
**Result:** The upload succeeded. This indicated that anyone on the internet could host malware or phishing content on this company's trusted domain (a "Subdomain Takeover" risk).

---

## 3. Cloud SSRF & IMDS Attacks
**Objective:** Exploit a web vulnerability to steal cloud credentials.

Server-Side Request Forgery (SSRF) is a critical cloud vulnerability. It allows an attacker to trick a server into making requests to internal resources—most notably, the **Instance Metadata Service (IMDS)**.

### Step 1: Identifying the SSRF
I encountered a web application that fetched images via a URL parameter (e.g., `?img_url=http://site.com/logo.png`).

### Step 2: The Exploit Payload
I replaced the image URL with the link to the local AWS IMDS. This IP address is only accessible from *inside* the EC2 instance.
*Payload:*
```
http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

### Step 3: Credential Theft
The server fetched the URL and returned the name of the IAM role attached to the instance (`WebRole`). I then queried that specific role:
```
http://169.254.169.254/latest/meta-data/iam/security-credentials/WebRole
```
**Result:** The server displayed the JSON response containing the temporary **AccessKeyId**, **SecretAccessKey**, and **Token**.

### Step 4: Using Stolen Keys
I configured my local environment with these keys to gain direct access to the victim's cloud account.
```bash
export AWS_ACCESS_KEY_ID=ASIA...
export AWS_SECRET_ACCESS_KEY=wJalr...
export AWS_SESSION_TOKEN=IQoJ...
aws sts get-caller-identity
```
**Impact:** I successfully authenticated as the `WebRole` and could now access any AWS resources (S3, DynamoDB, etc.) that the role had permission to touch.

---

## 4. Cloud Configuration Assessment
**Objective:** Audit a cloud environment for security best practices.

Offense informs defense. After learning how to exploit these flaws, I used automated tools to audit a simulated environment.

### Step 1: Running ScoutSuite
I ran `ScoutSuite`, a multi-cloud security-auditing tool, against the AWS account.
```bash
scout aws
```

### Step 2: Analysis
The tool generated an HTML report flagging critical issues:
*   **S3:** Buckets with global read/write permissions.
*   **IAM:** Users with no Multi-Factor Authentication (MFA) enabled.
*   **EC2:** Security Groups allowing SSH (port 22) from `0.0.0.0/0` (the entire internet).

**Takeaway:** Manual review of cloud settings is error-prone. Automated configuration monitoring is essential to maintain a secure posture in dynamic cloud environments.
