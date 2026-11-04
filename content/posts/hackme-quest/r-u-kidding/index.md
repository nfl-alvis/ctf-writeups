---
title: "r u kddding"
date: 2026-02-22
categories:
  - Crypto
tags:
  - Crypto
  - ctf.hackme.quest
collections:
  - ctf.hackme.quest
---


{{< admonition tldr "TL;DR" >}}

**r u kddding** is a crypto challenge from **ctf.hackme.quest**. EKZF{Hs'r snnn dzrx, itrs bzdrzq bhogdq}

{{< /admonition >}}

## Challenge Description

EKZF{Hs'r snnn dzrx, itrs bzdrzq bhogdq}

---

## Technical Analysis

Given a ciphertext which is the result of encryption **Caesar Cipher**.

From the flag format, it can be assumed that the prefix should be `FLAG{` . However, the ciphertext says `EKZF{` , so it can be concluded that the text has shifted **-1**.

To decrypt, the alphabet needs to be shifted by **+1**. This process can be done using `tr` with the following command:

```bash
echo "EKZF{Hs'r snnn dzrx, itrs bzdrzq bhogdq}" | tr 'a-zA-Z' 'b-zaB-ZA'
```
Output:

```txt
FLAG{It's tooo easy, just caesar cipher}
```

---

## Flags

```txt
FLAG{It's tooo easy, just caesar cipher}
```
