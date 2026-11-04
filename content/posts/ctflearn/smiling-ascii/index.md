---
title: "Smiling ASCII"
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

**Smiling ASCII** is a forensics challenge from **CTFlearn**. Find the flag on the smiling face.   [smiling.png](https://ctflearn.com/challenge/download/903)

{{< /admonition >}}

## Challenge Description

Find the flag on the smiling face.  
[smiling.png](https://ctflearn.com/challenge/download/903)

---

## Initial Analysis

The `smiling.png` file is opened visually for initial inspection. No visual irregularities such as hidden text, striking color differences, or suspicious artifacts were found.

{{< image src="./images/smiling.png" >}}

Because there are no visible clues, it is likely that information is hidden using **non-visual steganography** techniques.

---

## Technical Analysis of Hidden Data with Zsteg

The next step is to check the internal structure of the image using **zsteg**, a tool commonly used to detect hidden data in PNG files.

```bash
zsteg smiling.png
```

## Results Output

```
[?] 104 bytes of extra data after image end (IEND), offset = 0xe6cd
extradata:0         .. text: "RGlkIHlvdSBrbm93IHRoYXQgcGl4ZWxzIGFyZSwgbGlrZSB0aGUgYXNjaWkgdGFibGUsIG51bWJlcmVkIGZyb20gMCB0byAyNTU/Cg=="
```

You can see that there is **additional data after the IEND chunk**, which contains a string encoded using **Base64**.

---

## Decode Base64

The Base64 string is decoded using the following command:

```bash
echo 'RGlkIHlvdSBrbm93IHRoYXQgcGl4ZWxzIGFyZSwgbGlrZSB0aGUgYXNjaWkgdGFibGUsIG51bWJlcmVkIGZyb20gMCB0byAyNTU/Cg==' | base64 -d
```

Decode Results:

```
Did you know that pixels are, like the ascii table, numbered from 0 to 255?
```

This message functions as a **hint** , indicating that pixel values (0–255) can be directly mapped to ASCII characters. This means that the payload probably **does not use classic LSB techniques**, but instead utilizes pixel values ​​directly as character representation.

---

## Advanced Scan

Since the flag was not found in the initial scan, a thorough scan was performed using the `--all` option in zsteg.

```bash
zsteg --all smiling.png
```

## Relevant Results

```
b8,g,lsb,yx         .. text: "CTFlearn{ascii_pixel_flag}"
b8,a,lsb,yx         .. text: "CTFlearn{ascii_pixel_flag}"
```

On the **Green** and **Alpha** channels, with the `b8` (8-bit) configuration, a plaintext string was found which was clearly a flag.

---

##Flags

```text
CTFlearn{ascii_pixel_flag}
```
