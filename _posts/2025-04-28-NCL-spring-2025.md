---
layout: post
title: "NCL: Spring 2025 Competition"
date: 2025-04-28
categories: [CTF Labs, Competition]
tags: [National Cyber League, Linux, Wireshark]
---

## Overview

The **National Cyber League (NCL) Spring 2025** competition brought together over 8,500 individual competitors and almost 5,000 teams from across the nation, testing participants on their skills in various domains of cybersecurity. This event offered both an **Individual Game** and a **Team Game**, each designed to challenge competitors across a wide range of skills, including **Network Traffic Analysis**, **OSINT (Open Source Intelligence)**, **Web Exploitation**, **Forensics**, and more.

This write-up summarizes my individual and team performance, with emphasis on the network traffic analysis tasks I handled and the tools used to solve them.

---

## Individual Game Performance

In the **Individual Game**, I ranked **521st** out of 8,569 competitors, placing me in the **Top 6%**. This game consisted of solving individual challenges across multiple domains. The challenges tested my ability to think critically and apply my knowledge of tools like **Wireshark**, **Nmap**, **Gobuster**, **Python**, and **Linux CLI**.

### Challenges Solved

- **Reconnaissance**: I successfully completed challenges that required me to gather intelligence on targets, such as performing passive and active network scans.
- **OSINT (Open Source Intelligence)**: I analyzed publicly available information to identify potential vulnerabilities in targets, demonstrating the importance of open-source tools for threat analysis.
- **Web Exploitation**: I solved challenges that focused on finding and exploiting web vulnerabilities, including cross-site scripting (XSS) and SQL injection.
- **Forensics**: In the forensics category, I recovered data from compromised systems and investigated network traffic to uncover malicious activities.
- **Traffic Analysis**: I used tools like **Wireshark** to analyze network traffic, identifying anomalies and uncovering security breaches.

Tools and techniques I utilized:
- **Wireshark**: For network traffic analysis, including inspecting HTTP requests, response codes, and examining packet contents.
- **Nmap**: For network reconnaissance and vulnerability scanning.
- **Gobuster**: For directory brute-forcing to find hidden web directories.
- **Python**: Used for scripting tasks, including automating processes and parsing data from network traffic.
- **Linux CLI**: For system-level investigations and handling server-based challenges.

---

## Team Game Performance

In the **Team Game**, my team ranked **63rd** out of 4,798 teams, placing us in the **Top 1.3%**. This portion of the competition involved collaborative problem-solving, where we worked together to tackle complex challenges. My primary contribution was in the area of **Network Traffic Analysis**, where I applied advanced filtering techniques in **Wireshark** to uncover key data.

### My Role in the Team

- **Network Traffic Analysis**: I was responsible for analyzing **PCAP** files and identifying specific patterns and anomalies in network traffic. Using **Wireshark**, I filtered traffic to extract valuable information about servers, IP addresses, and HTTP responses.
- **OSINT**: I also contributed to **OSINT** challenges, helping the team improve its overall score.
- **Collaboration**: I worked closely with my teammates to develop strategies for solving challenges, ensuring we stayed on track and optimized our approach.

---

## Key Challenges I Solved for My Team

### 1. **Q1: How many response packets included a status code other than ‘200 OK’?**
   - **Methodology**: I used **Wireshark** to open the `httpFOREVER.pcapng` file and applied the filter `http.response.code != 200` to identify packets that had status codes other than "200 OK".
   - **Answer**: 2 packets.
   - **Detailed Analysis**: I discovered that 17 packets had the status code "301 Moved Permanently", and 55 packets had the status code "200 OK". This helped us confirm the behavior of the target server and its response handling.

### 2. **Q2: What server and version are used for the site at 146.190.62.39?**
   - **Methodology**: I applied a Wireshark filter to isolate HTTP responses from the IP address `146.190.62.39`. The filter `ip.src == 146.190.62.39 && http.response` helped me identify the HTTP responses sent by the server.
   - **Answer**: Server: **nginx/1.18.0 (Ubuntu)**.
   - **Explanation**: This server was running **nginx** version **1.18.0** on an **Ubuntu** OS, which was crucial for our team to understand the server environment.

### 3. **Q3: What is the domain for the website with `/icons/favicon-red.ico`?**
   - **Methodology**: I filtered Wireshark for `http.request.uri == "/icons/favicon-red.ico"`, then extracted the domain name from the **Host** header in the HTTP request.
   - **Answer**: Host: **http.badssl.com**.
   - **Importance**: This challenge was a simple yet effective way to examine how websites handle static content and identify potential security risks based on the domain.

### 4. **Q4: What IP address was accessed using Chrome?**
   - **Methodology**: I applied a filter for HTTP requests with the **User-Agent** containing "Chrome" (`http.request && http.user_agent contains "Chrome"`), allowing me to isolate traffic from the Chrome browser.
   - **Answer**: Destination Address: **188.184.67.127**.
   - **Insight**: Identifying which IP address was accessed using Chrome allowed us to track the behavior of specific client devices, providing insights into targeted attacks.

### 5. **Q5: How many unique IP addresses did the client make requests to?**
   - **Methodology**: I filtered Wireshark to find packets originating from the client’s IP (`ip.src == 192.168.232.132`), and then used Wireshark’s **Statistics -> Endpoints -> IPv4** feature to count the unique destination IP addresses.
   - **Answer**: **5 unique IPs**.
   - **Significance**: This information helped us analyze the communication patterns of the client and detect any abnormal network behavior.

---

## Conclusion

The **National Cyber League (NCL) Spring 2025** competition provided a structured environment to apply security analysis, network investigation, and web security skills under time pressure. I used tools including **Wireshark**, **Nmap**, and **Python** to solve challenges across several domains.

This experience strengthened my workflow for **network traffic analysis** and **OSINT**, while reinforcing practical knowledge in web security and forensics. My individual top 6% placement and the team's top 1.3% placement reflect consistent performance across both competition formats.

The competition also produced portfolio-relevant evidence of analysis discipline: filtering packet captures, extracting server metadata, correlating client behavior, and documenting findings clearly.
