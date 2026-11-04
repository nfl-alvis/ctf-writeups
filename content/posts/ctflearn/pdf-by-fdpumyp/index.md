---
title: "PDF by fdpumyp"
date: 2026-02-22
categories:
  - Forensics
tags:
  - Forensics
  - CTFlearn
collections:
  - CTFlearn
---



{{< admonition tldr "TL;DR" >}}

**PDF by fdpumyp** is a forensics challenge from **CTFlearn**. Hi, just as we talked during a break, you have this file here and check if something is wrong with it. That's the only thing we found strange with this suspect, I hope there will be a password for his external drive Bye [dontopen.pdf](https://ctflearn.com/challenge/download/957)

{{< /admonition >}}

## Challenge Overview

Hi, just as we talked during a break, you have this file here and check if something is wrong with it. That's the only thing we found strange with this suspect, I hope there will be a password for his external drive  
  
Bye  
  
[dontopen.pdf](https://ctflearn.com/challenge/download/957)

---

## Technical Analysis

### 1. Initial File Check

The file `dontopen.pdf` is opened statically without executing it.
To look for possible **hidden data in plaintext**, an analysis was conducted using the command `strings` .

```bash
strings dontopen.pdf
```

---

### 2. Finding Suspicious Information

From the result of the `strings` command, several lines are found that are clearly unusual for a regular PDF file:

```text
== SECRET DATA DONT LOOK AT THIS ==
external:Q1RGbGVhcm57KV8xbDB3M3kwVW0wMG15MTIzfQ==
pin:1234
password:MTIzMVdST05HOWlzamRuUEFTU1dPUkQ=
```

It can be seen that:

* There are several sensitive fields ( `external` , `pin` , `password` )
* Values in the `external` and `password` fields appear to use **Base64 encoding**

---

### 3. Base64 decoding

The main focus is the `external` field, because it contains the CTF flag pattern.

Base64 decoding is carried out using the following command:

```bash
echo 'Q1RGbGVhcm57KV8xbDB3M3kwVW0wMG15MTIzfQ==' | base64 -d
```

Decoding result:

```text
CTFlearn{)_1l0w3y0Um00my123}
```

---

##Flags

```text
CTFlearn{)_1l0w3y0Um00my123}
```
