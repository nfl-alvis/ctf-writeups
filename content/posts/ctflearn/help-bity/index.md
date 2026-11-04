---
title: "Help Bity"
date: 2026-02-22
categories:
  - Misc
tags:
  - Misc
  - CTFlearn
collections:
  - CTFlearn
---


{{< admonition tldr "TL;DR" >}}

**Help Bity** is a misc challenge from **CTFlearn**. Bity had the flag for his problem. Unfortunately, his negative friend Noty corrupted it. Help Bity retrieve his flag.   He only remembers the first 4 characters of the flag: CTFL. Flags:

```BUGMd\`sozc0o\`sx^0r^\`vdr1ld|```

{{< /admonition >}}

Bity had the flag for his problem. Unfortunately, his negative friend Noty corrupted it. Help Bity retrieve his flag.  
He only remembers the first 4 characters of the flag: CTFL. Flags:

```BUGMd\`sozc0o\`sx^0r^\`vdr1ld|```

---

## Technical Analysis

Given a string whose bits have been modified, trying to use cyberchef with _recipe Magic_ with _intensive mode_ active and _known plaintext_ namely `CTFL` gets the following output:

{{< image src="./images/decoded.png" >}}

From the output above it is known that the ciphertext is the result of encryption using the **XOR** operation with _key_ 1.  

---

## Flags

```CTFLearn{b1nary_1s_awes0me}```
