# PicoCTF 2026 – General Skills: Printer Shares



{{< admonition tldr "TL;DR" >}}

**picoCTF 2026 – General Skills: Printer Shares** is a misc challenge from **picoCTF**. This challenge simulates a scenario where an important file was accidentally sent to a network printer. The goal is to retrieve the file from the printer server. The hints suggest that understanding the **SMB (Server Message Block)** protocol and using tools such as `smbclient` or `smbutil` will be helpful.

{{< /admonition >}}

## Overview

This challenge simulates a scenario where an important file was accidentally sent to a network printer. The goal is to retrieve the file from the printer server. The hints suggest that understanding the **SMB (Server Message Block)** protocol and using tools such as `smbclient` or `smbutil` will be helpful.

SMB is a network protocol commonly used for file sharing between systems. Many printers expose shared directories for storing or processing print jobs.

---

## Initial Reconnaissance

The challenge provides the following instructions:

```
Oops! Someone accidentally sent an important file to a network printer—can you retrieve it from the print server?
```

We are given the host and port:

```
mysterious-sea.picoctf.net
50356
```

First, verify that the port is open.

```bash
nc -vz mysterious-sea.picoctf.net 50356
```

Output:

```
mysterious-sea.picoctf.net [3.130.79.223] 50356 (?) open
```

This confirms that a service is listening on port `50356` .

---

## Enumerating SMB Shares

Based on the hints mentioning SMB tools, the next step is to attempt connecting using `smbclient` .

```bash
smbclient //mysterious-sea.picoctf.net/shares -p 50356 -N
```

Explanation:

* `//host/share` specifies the SMB share.
* `-p` sets the custom port used by the SMB service.
* `-N` attempts an anonymous login (no password).

After connecting successfully, an SMB shell is opened.

---

## Listing Available Files

Within the SMB shell, list the files in the share:

```bash
ls
```

Output:

```
.            D        0
..           D        0
dummy.txt    N     1142
flag.txt     N       37
```

Two files are available:

* `dummy.txt` * `flag.txt` ---

## Downloading the Files

Files can be retrieved using the `get` command.

```bash
get flag.txt
get dummy.txt
```

Example output:

```
getting file \flag.txt of size 37 as flag.txt
getting file \dummy.txt of size 1142 as dummy.txt
```

---

## Inspecting the Files

First, check the contents of `dummy.txt` .

```bash
cat dummy.txt
```

This file contains placeholder text and does not contain the flag.

Next, inspect the flag file.

```bash
cat flag.txt
```

Output:

```
picoCTF{5mb_pr1nter_5h4re5_8a0df8e0}
```

---

## Final Flags

```
picoCTF{5mb_pr1nter_5h4re5_8a0df8e0}
```

---

## Key Takeaways

* SMB (Server Message Block) is widely used for network file sharing.
* Services can run on non-standard ports in CTF environments.
* Tools like `smbclient` allow interaction with SMB shares directly from the command line.
* Anonymous SMB access can expose sensitive files if permissions are misconfigured.
* Enumerating shared directories is a crucial step during network reconnaissance.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/printer-shares/  

