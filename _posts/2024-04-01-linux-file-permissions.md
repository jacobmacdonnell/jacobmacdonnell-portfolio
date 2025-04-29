---
title: Editing File Permissions in Linux
date: 2024-04-01
categories: [File Permissions]
tags: []
---

## Project Overview

In this project, I demonstrated how to manage and update file permissions in a Linux environment. File permissions are a critical aspect of system security, determining who can read, write, or execute files on a Linux system.

The goal was to properly configure permissions to ensure that only authorized users could access or modify sensitive files, a fundamental principle of cybersecurity.

## Scenario

As a security professional, I was tasked with updating file permissions for a set of sensitive files on a Linux server. The permissions needed to be configured according to the principle of least privilege, ensuring that:

- Regular users could only access files they needed for their work
- System administrators had appropriate access
- Some files were protected from modification by any user

## Implementation

The following commands were used to check and modify file permissions:

### Checking Current Permissions

First, I checked the current permissions of the files:

```bash
jacob@Jacobs-PC:~ $ ls -l /home/data/
-rw-rw-r-- 1 admin staff 8425 Apr 1 08:30 financial_report.txt
-rwx------ 1 admin admin 2048 Apr 1 08:30 system_backup.sh
-rw-r--r-- 1 admin staff 1650 Apr 1 08:30 user_list.txt
```

### Understanding Permission Notation

Linux file permissions are represented by a string of characters:
- First character: File type (`-` for regular file, `d` for directory)
- Next three characters: Owner permissions (`r`=read, `w`=write, `x`=execute)
- Next three characters: Group permissions
- Last three characters: Others permissions

### Modifying Permissions

I applied the following changes:
- Restricted access to the financial report so only the owner can write to it
- Ensured system administrators can execute the backup script
- Made the user list readable but not writeable by regular users

### Using chmod Command

The `chmod` command is used to change file permissions. I used it with both symbolic and numeric notation:

```bash
jacob@Jacobs-PC:~ $ chmod u=rw,g=r,o=r /home/data/financial_report.txt
jacob@Jacobs-PC:~ $ chmod 750 /home/data/system_backup.sh
jacob@Jacobs-PC:~ $ chmod 444 /home/data/user_list.txt
```

### Changing Ownership

I also needed to change ownership of some files using the `chown` command:

```bash
jacob@Jacobs-PC:~ $ sudo chown admin:sysadmin /home/data/system_backup.sh
```

### Verifying Changes

After making all changes, I verified the new permissions:

```bash
jacob@Jacobs-PC:~ $ ls -l /home/data/
-rw-r--r-- 1 admin staff    8425 Apr 1 08:30 financial_report.txt
-rwxr-x--- 1 admin sysadmin 2048 Apr 1 08:30 system_backup.sh
-r--r--r-- 1 admin staff    1650 Apr 1 08:30 user_list.txt
```

## Security Implications

By implementing these permission changes, I achieved the following security benefits:

1. **Least Privilege**: Users only have access to what they need for their jobs
2. **Data Protection**: Sensitive financial data can only be modified by authorized users
3. **System Security**: System scripts can only be executed by administrators
4. **Information Control**: Crucial information is available for reference but protected from unauthorized changes

This implementation demonstrates the importance of proper file permissions in maintaining a secure Linux environment. By understanding and correctly applying file permission concepts, organizations can significantly reduce their attack surface and protect sensitive data from unauthorized access or modification.

## Best Practices for Linux File Permissions

Based on this project, I recommend the following best practices:

- Regularly audit file permissions to ensure they remain appropriate
- Use group permissions effectively to manage access for different teams
- Remove write access from files that should not be modified
- Restrict execute permissions to only necessary files and users
- Consider using Access Control Lists (ACLs) for more granular control when needed