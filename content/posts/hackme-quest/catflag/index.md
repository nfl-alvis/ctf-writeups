---
title: "Catflag"
date: 2026-02-11
categories:
  - Pwn
tags:
  - Netcat
  - TCP Socket
  - Linux Commands
  - Shell Access
collections:
  - ctf.hackme.quest
---



> **Connect to the remote server using Netcat and capture the flag!**

{{< admonition tldr "TL;DR" >}}

**Catflag** is a beginner-friendly interaction challenge introducing network socket connections using `netcat` . Upon connecting to TCP port `7709` , the server gives a 5-second countdown before dropping the connection into an interactive restricted shell. Listing the working directory and printing `flag` reveals the solution.

{{< /admonition >}}

## Analysis & Interaction

We establish a raw TCP connection to the service using `nc` :

```bash
nc ctf.hackme.quest 7709
```

The server displays an initial countdown message:

```text
plz capture the flag after 5 seconds...
plz capture the flag after 4 seconds...
plz capture the flag after 3 seconds...
plz capture the flag after 2 seconds...
plz capture the flag after 1 seconds...
```

Once the timer expires, the remote server opens an interactive terminal session.

---

## Command Execution

We list the contents of the current working directory:

```bash
ls -la
```

```text
total 16
drwxr-xr-x 1 user user 4096 Feb 11 14:00 .
drwxr-xr-x 1 root root 4096 Feb 11 14:00 ..
-r-------- 1 user user   26 Feb 11 14:00 flag
-r-xr-xr-x 1 user user  120 Feb 11 14:00 run.sh
-r-xr-xr-x 1 user user 1024 Feb 11 14:00 shell
```

We read the flag file using `cat` :

```bash
cat flag
```

Output:

```text
FLAG{cat flag? dog flag!}
```

---

##Flags

```text
FLAG{cat flag? dog flag!}
```
