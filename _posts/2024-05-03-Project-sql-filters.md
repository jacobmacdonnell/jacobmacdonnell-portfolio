---
title: "Applying Filters to SQL Queries"
date: 2024-05-03
categories: [Projects, Data Analysis]
tags: [SQL, Linux]
---

## Project Overview

In this project, I demonstrated how to effectively use SQL filters, operators, and keywords to extract specific security-relevant data from large databases. SQL filtering is a crucial skill for cybersecurity analysts who need to sift through large datasets to identify potential security incidents.

By applying specific filters to SQL queries, security professionals can isolate suspicious activities, detect anomalies, and generate targeted reports from security logs and databases.

## Scenario

As a security analyst, I was tasked with analyzing a database of network traffic logs to identify potential security incidents. The database contained millions of records, making manual analysis impractical. My objective was to:

- Filter traffic from specific IP addresses flagged as suspicious
- Identify unusual access patterns during non-business hours
- Generate summary reports of failed login attempts
- Detect potential data exfiltration by analyzing outbound traffic volumes

## Implementation

To effectively analyze the security data, I utilized various SQL filtering techniques:

### Basic Filtering with WHERE Clause

Query to identify all traffic from a suspicious IP address:

```sql
mysql> SELECT * 
    -> FROM network_logs 
    -> WHERE source_ip = '192.168.1.137'
    -> ORDER BY timestamp DESC;
```

34 rows returned (showing first 3)

| id      | timestamp           | source_ip     | destination_ip | port  | protocol | bytes_sent |
|---------|---------------------|---------------|----------------|-------|----------|------------|
| 1082437 | 2024-05-03 02:14:28 | 192.168.1.137 | 203.0.113.42   | 22    | SSH      | 4096       |
| 1082302 | 2024-05-03 02:12:15 | 192.168.1.137 | 203.0.113.42   | 22    | SSH      | 8192       |
| 1082105 | 2024-05-03 01:57:33 | 192.168.1.137 | 203.0.113.42   | 22    | SSH      | 2048       |

### Using Multiple Conditions with AND/OR

Identify potential unauthorized access during non-business hours:

```sql
mysql> SELECT * 
    -> FROM access_logs 
    -> WHERE (HOUR(access_time) < 8 OR HOUR(access_time) > 18)
    -> AND access_status = 'success'
    -> AND user_role != 'admin'
    -> ORDER BY access_time;
```

17 rows returned (showing first 3)

| log_id  | user_id | access_time         | resource_accessed | access_status | user_role |
|---------|---------|---------------------|-------------------|---------------|-----------|
| 45291   | user047 | 2024-05-02 05:12:47 | /admin/config     | success       | standard  |
| 45387   | user023 | 2024-05-02 19:34:12 | /reports/security | success       | standard  |
| 45412   | user047 | 2024-05-02 22:03:19 | /admin/users      | success       | standard  |

### Using the LIKE Operator for Pattern Matching

Find attempts to access sensitive directories:

```sql
mysql> SELECT COUNT(*) as attempt_count, user_id, resource_accessed
    -> FROM access_logs
    -> WHERE resource_accessed LIKE '%/admin/%' 
    -> OR resource_accessed LIKE '%/config/%'
    -> OR resource_accessed LIKE '%/secure/%'
    -> GROUP BY user_id, resource_accessed
    -> HAVING attempt_count > 5
    -> ORDER BY attempt_count DESC;
```

4 rows returned

| attempt_count | user_id | resource_accessed |
|---------------|---------|-------------------|
| 27            | user047 | /admin/config     |
| 14            | user023 | /admin/users      |
| 8             | user015 | /secure/keys      |
| 6             | user047 | /config/database  |

### Advanced Filtering with Subqueries

Identify users with failed login attempts followed by successful access:

```sql
mysql> SELECT DISTINCT l1.user_id, l1.ip_address, l1.timestamp 
    -> as failed_time, l2.timestamp as success_time
    -> FROM login_logs l1
    -> JOIN login_logs l2 ON l1.user_id = l2.user_id AND l1.ip_address = l2.ip_address
    -> WHERE l1.status = 'failed' 
    -> AND l2.status = 'success'
    -> AND l2.timestamp > l1.timestamp
    -> AND TIMESTAMPDIFF(MINUTE, l1.timestamp, l2.timestamp) < 30
    -> ORDER BY l1.user_id, l1.timestamp;
```

7 rows returned (showing first 3)

| user_id | ip_address    | failed_time         | success_time         |
|---------|---------------|---------------------|----------------------|
| user015 | 192.168.1.54  | 2024-05-03 14:23:42 | 2024-05-03 14:24:15  |
| user023 | 192.168.1.78  | 2024-05-02 19:30:58 | 2024-05-02 19:34:12  |
| user047 | 192.168.1.137 | 2024-05-03 01:45:27 | 2024-05-03 01:47:38  |

## Security Findings

Through effective SQL filtering, I was able to identify several security concerns:

### Key Findings

- **Potential Brute Force Attempts**: Identified multiple failed login attempts followed by successful logins within short time periods
- **Unauthorized Access Patterns**: Detected unusual access patterns from non-admin users attempting to access sensitive resources
- **Data Exfiltration Indicators**: Found suspicious outbound data transfers during non-business hours

These findings triggered a more in-depth security investigation, which revealed that user047's account had been compromised and was being used to exfiltrate sensitive information.

## Conclusion

SQL filtering is a powerful tool in a cybersecurity analyst's arsenal. By effectively applying SQL filters, I was able to quickly sift through millions of log entries to identify suspicious patterns that would have been impossible to detect manually.

This project demonstrated my ability to:

- Write complex SQL queries with multiple filtering conditions
- Use pattern matching to identify potential security concerns
- Analyze temporal relationships in security data
- Generate actionable security intelligence from raw log data

These SQL skills are directly applicable to various cybersecurity roles, including security operations, threat hunting, and incident response.
