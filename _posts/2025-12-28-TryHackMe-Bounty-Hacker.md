---
title: TryHackMe Bounty Hacker Writeup
date: 2025-12-28
categories: [CTF Competitions, TryHackMe]
tags: [CTF Challenges, Linux, Privilege Escalation, FTP, SSH]
---

## Challenge Overview

**Bounty Hacker** is a beginner-friendly TryHackMe room focused on basic enumeration, credential reuse, and simple privilege escalation through misconfiguration.

## Enumeration

I started with basic enumeration to see what the target was exposing.

```bash
nmap -p- 10.67.179.231
```

The scan showed three open ports: FTP (21), SSH (22), and HTTP (80). I followed up with a service scan to confirm what was running.

```bash
nmap -sV -p 21,22,80 10.67.179.231
```

No obvious version-based exploits stood out, so I moved on to manually testing services.

## FTP Enumeration

I checked FTP first since it is quick to test.

```bash
ftp 10.67.179.231
```

Anonymous login worked. That immediately made FTP worth investigating further.

Listing the directory showed two files: `task.txt` and `locks.txt`. I downloaded both.

- **task.txt** contained short notes and was signed with `-lin`, which I treated as a likely username.
- **locks.txt** contained a list of password-like strings. It clearly resembled a wordlist.

At this point, I had a probable username, a password list, and an exposed SSH service.

## SSH Access

Using the information from FTP, I attempted SSH authentication with Hydra.

```bash
hydra -l lin -P locks.txt ssh://10.67.179.231
```

Hydra returned valid credentials:

```
lin : RedDr4gonSynd1cat3
```

I logged in via SSH and found the user flag on the desktop.

```bash
cat user.txt
```

## Privilege Escalation

After gaining user access, I checked sudo permissions.

```bash
sudo -l
```

The user was allowed to run `/bin/tar` as root. I knew tar could be abused for privilege escalation, but I did not remember the exact syntax, so I checked [GTFOBins](https://gtfobins.github.io/gtfobins/tar/) to confirm the correct command.

Using tar's checkpoint feature, I executed a root shell:

```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```

This dropped me into a root shell. From there, I navigated to `/root/root.txt` and retrieved the final flag.

## Takeaway

This room was a good example of how small misconfigurations chain together:

1. **Anonymous FTP access** exposed sensitive files
2. **Credentials were reused** across services
3. **An unsafe sudo rule** allowed full privilege escalation

Nothing advanced was required. Just enumeration and following what the system exposed.
