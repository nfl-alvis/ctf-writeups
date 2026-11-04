---
title: "Tux!"
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

**Tux!** is a forensics challenge from **CTFlearn**. ---

{{< /admonition >}}

---

##Challenge

The flag is hidden inside the Penguin! Solve this challenge before solving my 100 point Scope challenge which uses similar techniques as this one.  
[Tux.jpg](https://ctflearn.com/challenge/download/973)

---

## Technical Analysis

We are provided with an image file named `Tux.jpg` .

{{< image src="./images/Tux.jpg" >}}

The first step is to examine the **metadata** of the image for hidden information. From the results of the metadata analysis, a string **Base64** was found in the *Comment* section:

```
Comment : ICAgICAgUGFzc3dvcmQ6IExpbnV4MTIzNDUK
```

---

## Decode Metadata

The Base64 string is decoded and produces the following plaintext:

```
Password: Linux12345
```

At this stage it is not clear what the password is used for, so further analysis of the image file structure is needed.

---

## Hidden File Extraction

Next, an analysis was conducted with **binwalk** to detect any files embedded in the image:

```bash
binwalk -e Tux.jpg
```

The results show the presence of an encrypted ZIP archive:

```
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
5488          0x1570          Zip archive data, encrypted at least v1.0 to extract,
                             compressed size: 39, uncompressed size: 27, name: flag
```

This indicates that the image file contains a **hidden ZIP archive** which is protected by a password.

---

## ZIP and Flag extraction

The extracted ZIP is then opened. When asked for a password, the password obtained from the previous metadata is used:

```
Password: Linux12345
```

The extraction process was successful and produced a file named `flag` .

To read the contents:

```bash
cat flag
```

Output:

```
CTFlearn{Linux_Is_Awesome}
```

---

##Flags

```text
CTFlearn{Linux_Is_Awesome}
```
