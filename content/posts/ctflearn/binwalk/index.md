---
title: "Binwalk"
date: 2026-02-22
categories:
  - Forensics
tags:
  - File Carving
  - Steganography
  - Exiftool
  - Foremost
  - Binwalk
collections:
  - CTFlearn
---



> **Here is a file with another file hidden inside it. Can you extract it?**

{{< admonition tldr "TL;DR" >}}

**Binwalk** is an introductory digital forensics challenge that focuses on file carving and detecting embedded artifacts within image containers. Inspecting the file headers reveals an anomaly where a PNG file is masquerading under a `.jpeg` extension with extra data trailing the `IEND` chunk. Carving the hidden binary via `foremost` or `binwalk` extracts a secondary image displaying the flag.

{{< /admonition >}}

## File Analysis

We are given an image file named `PurpleThing.jpeg` .

{{< image src="./images/chall.webp" >}}

We begin by examining the file's metadata and structure using `exiftool` :

```bash
exiftool PurpleThing.jpeg
```

```text
ExifTool Version Number         : 13.25
File Name                       : PurpleThing.jpeg
File Size                       : 165 kB
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 780
Image Height                    : 720
Warning                         : [minor] Trailer data after PNG IEND chunk
```

Two critical anomalies stand out:

1. **Mismatched File Extension:** Despite having a `.jpeg` extension, the magic bytes identify the container as a **PNG** image.
2. **Trailer Data Warning:** ExifTool reports `[minor] Trailer data after PNG IEND chunk` .

In the PNG file specification, the `IEND` chunk marks the valid end of the image stream. Any bytes appended after `IEND` represent appended or concatenated data—frequently indicating an embedded file payload.

---

## Carving the Embedded Data

We can carve embedded files out of the container using file carving tools such as `foremost` or `binwalk` :

```bash
foremost PurpleThing.jpeg
```

`foremost` automatically scans for known headers and footers, extracting two distinct PNG files into `output/png/` :

```text
00000000.png  00000299.png
```

- `00000000.png` represents the original outer image.
- `00000299.png` is the hidden payload embedded at offset `299` .

Opening `00000299.png` reveals the flag:

{{< image src="./images/flag.webp" >}}

---

##Flags

```text
ABCTF{b1nw4lk_is_us3ful}
```

---

## Alternative One-Liner Solution

The same carving operation can also be completed directly with `binwalk` :

```bash
binwalk -e PurpleThing.jpeg
```
