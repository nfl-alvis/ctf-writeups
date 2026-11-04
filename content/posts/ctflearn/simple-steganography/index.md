---
title: "Simple Steganography"
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

**Simple Steganography** is a forensics challenge from **CTFlearn**. Think the flag is somewhere in there. Would you help me find it? hint-" Steghide Might be Helpful" [Minions1.jpg](https://ctflearn.com/challenge/download/894)

{{< /admonition >}}

## Challenge Overview

Think the flag is somewhere in there. Would you help me find it? hint-" Steghide Might be Helpful"  
[Minions1.jpg](https://ctflearn.com/challenge/download/894)

---

## Initial Analysis

The file `Minions1.jpg` is opened visually and does not show any hidden indications on the image display.

The next step is to perform metadata analysis using `exiftool` to look for non-visual information.

```bash
exiftool Minions1.jpg
```

From the result metadata, suspicious information was found:

```text
Keywords : myadmin
```

This value is thought to be a **passphrase** for the steganography extraction process.

---

## Hidden Data Extraction

By using the hint challenge and keywords found, extraction is carried out using `steghide` :

```bash
steghide extract -sf Minions1.jpg
```

When asked for a passphrase, use:

```
myadmin
```

The extraction process was successful and produced the following files:

```
raw.txt
```

---

## Technical Analysis of Extracted Files

The contents of `raw.txt` are checked:

```bash
cat raw.txt
```

Output:

```text
AEMAVABGAGwAZQBhAHIAbgB7AHQAaABpAHMAXwBpAHMAXwBmAHUAbgB9
```

The string is identified as **Base64 encoding**.

---

## Base64 decode

The decoding process is carried out using the following command:

```bash
echo 'AEMAVABGAGwAZQBhAHIAbgB7AHQAaABpAHMAXwBpAHMAXwBmAHUAbgB9' | base64 -d
```

Decoding result:

```text
CTFlearn{this_is_fun}
```

---

##Flags

```text
CTFlearn{this_is_fun}
```
