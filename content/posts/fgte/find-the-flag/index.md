---
title: "Find The Flag"
date: 2026-02-22
categories:
  - Misc
tags:
  - Misc
  - FGTE
collections:
  - FGTE
---



{{< admonition tldr "TL;DR" >}}

**Find The Flag** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description
This challenge provides a file named `find_the_flag.dump` . In appearance the file looks like plain text, but the flag was not discovered by direct search using `strings` or `grep` .

The objective of the challenge is to find the flag hidden in the file.

---

## Solution Steps

### 1. Initial Check (Strings and File Type)

The initial step is to try to find the flag directly:

```bash
strings find_the_flag.dump | grep FGTE
```

There is no output, meaning the flag is not stored in plain text.

Next, check the file type:

```bash
file find_the_flag.dump
```

Output:

```
find_the_flag.dump: ASCII text
```

This shows the file looks like normal text, but is clearly not normal text because the result is not readable as a sentence.

---

### 2. File Structure Analysis

To ensure the data structure, an initial check of the file is carried out with `xxd` :

```bash
xxd find_the_flag.dump | head
```

Output:

```
00000000: 3159 4346 4748 674e 5862 6d43 5471 397a  1YCFGHgNXbmCTq9z
00000010: 574a 5347 4b50 4171 4243 3832 3246 6247  WJSGKPAqBC822FbG
00000020: 4d43 5363 7672 7070 6731 756c 4653 6974  MCScvrppg1ulFSit
00000030: 4662 7a4b 6e79 322f 7833 6936 3844 596c  FbzKny2/x3i68DYl
00000040: 4e4c 7364 7631 5956 576f 7a61 0a71 374d  NLsdv1YVWoza.q7M
00000050: 6a58 6c6c 3338 6642 6737 7351 532b 5736  jXll38fBg7sQS+W6
00000060: 3559 5557 456c 6f4b 4543 6863 7757 7547  5YUWEloKEChcwWuG
00000070: 3533 4a37 5a39 4b58 7353 4c77 4b39 4552  53J7Z9KXsSLwK9ER
00000080: 4365 6e4a 4c42 7544 5069 4d44 6445 7647  CenJLBuDPiMDdEvG
00000090: 6477 524a 6f62 414e 590a 3470 2b57 3035  dwRJobANY.4p+W05
```

The hex results show that the contents of the file consist of characters common to Base64, such as:

* Letters A–Z, a–z
*Numbers 0–9
* Symbols `+` and `/` Additionally, the contents of the file look like long blocks without normal text formatting, leading to a strong indication that the file is **Base64 encoded** data.

---

### 3. Decode Base64 Into Binary File

Since the file is very similar to Base64 format, the next step is to decode the entire contents of the file into a binary file.

```bash
cat find_the_flag.dump | base64 -d > output.bin
```

---

### 4. Look for the flag in the decoded file

After successful decoding, a search for the flag string is carried out in the decoded binary file.

```bash
strings output.bin | grep FGTE
```

Output:

```
FGTE{REDACTED}
```

---

##Flags

```
FGTE{REDACTED}
```
