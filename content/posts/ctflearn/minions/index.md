---
title: "Minions"
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

**Minions** is a forensics challenge from **CTFlearn**. Hey! Minions have stolen my flag, encoded it several times in one cipher, and then hidden it somewhere there: [Hey_You.png](https://mega.nz/file/1UBViYgD#kjKISs9pUB4E-1d79166FeX3TiY5VQcHJ_GrcMbaLhg) Can you help me?   TIP: Decode the flag until you get a sentence.

{{< /admonition >}}

## Challenge Overview

Hey! Minions have stolen my flag, encoded it several times in one cipher, and then hidden it somewhere there:  
[Hey_You.png](https://mega.nz/file/1UBViYgD#kjKISs9pUB4E-1d79166FeX3TiY5VQcHJ_GrcMbaLhg) Can you help me?  
TIP: Decode the flag until you get a sentence.

---

## Technical Analysis & Exploitation

### 1. Initial Analysis of File `Hey_You.png` The `Hey_You.png` file is opened visually and shows no suspicious indications:

{{< image src="./images/Hey_You.png" >}}

Since there are no visual clues, the next step is to look for **hidden data (embedded plaintext)** using the command `strings` .

```bash
strings Hey_You.png
```

From the output, a suspicious URL was found:

```
https://mega.nz/file/wZw2nAhS#i3Q0r-R8psiB8zwUrqHTr661d8FiAS1Ott8badDnZkoH
```

---

### 2. Second File: `Only_Few_Steps.jpg` The URL leads to the download of an image file named `Only_Few_Steps.jpg` .

{{< image src="./images/Only_Few_Steps.jpg" >}}

Visually, this image also does not contain any suspicious information. Return an analysis was conducted using `strings` .

```bash
strings Only_Few_Steps.jpg
```

From the result output found another file name:

```
YouWon(Almost).jpg
```

This indicates that there are **other files embedded** in `Only_Few_Steps.jpg` .

---

### 3. Hidden File Extraction

To extract embedded files, the `binwalk` tool is used.

```bash
binwalk -e Only_Few_Steps.jpg
```

The extraction results produce the following folder structure:

```
_Only_Few_Steps.jpg.extracted
├── 22806.rar
└── YouWon(Almost).jpg
```

The relevant file for further analysis is `YouWon(Almost).jpg` .

---

### 4. Analyze `YouWon(Almost).jpg` The `YouWon(Almost).jpg` file shows no suspicious visual clues:

![YouWon(Almost).jpg](_Only_Few_Steps.jpg.extracted/YouWon\(Almost\).jpg)

Followed by `strings` analysis to find hidden plaintext.

```bash
strings YouWon\(Almost\).jpg | head
```

Found a string with format flags whose contents are still encrypted:

```
CTF{VmtaU1IxUXhUbFZSYXpsV1RWUnNRMVpYZEZkYWJFWTJVVmhrVlZGVU1Eaz0=}
```

---

### 5. Decoding Process

The contents of the flag are visible using **Base64 encoding**.
Base64 decoding is carried out repeatedly until readable plaintext is obtained.

Final decoding result:

```
M1NI0NS_ARE_C00L
```

---

##Flags

```text
CTF{M1NI0NS_ARE_C00L}
```
