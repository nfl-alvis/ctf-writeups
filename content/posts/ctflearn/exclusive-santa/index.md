---
title: "Exclusive Santa"
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

**Exclusive Santa** is a forensics challenge from **CTFlearn**. > *"Dear Santa, > There are so many toys that I want, but I just don't have the money. > I don't care which toy I get as long as it's one or the other, but not both!"* > — CTFlearn [Exclusive_Santa.rar](https://ctflearn.com/challenge/download/851)

{{< /admonition >}}

##Challenge

> *“Dear Santa,
> There are so many toys that I want, but I just don't have the money.
> I don't care which toy I get as long as it's one or the other, but not both!”*
> — CTFlearn  
  
[Exclusive_Santa.rar](https://ctflearn.com/challenge/download/851)

---

## Initial Analysis

The first step is to extract the RAR archive:

```bash
unrar x Exclusive_Santa.rar
```

Extraction results:

```
Extracting  3.png   OK
Extracting  1.png   OK
All OK
```

There are two image files: ** `1.png` ** and ** `3.png` **.

---

## Technical Analysis Image

### 1️ Analyze `1.png` When opened, the `1.png` image displays an RGB color pattern that appears random and does not form a clear object. Patterns like this often indicate data that has been manipulated bitwise.

{{< image src="./images/1.png" >}}

### 2️ Analyze `3.png` In contrast to `1.png` , the `3.png` image appears visually normal and contains clues. This image dictates that certain colors or channels need to be combined.

{{< image src="./images/3.png" >}}

For further analysis, **zsteg** is used:

```bash
zsteg 3.png
```

Important output:

```
[?] 1169169 bytes of extra data after image end (IEND)
extradata:0 .. file: PNG image data, 1280 x 720, 8-bit/color RGBA
```

This indicates that there is **another PNG file embedded** inside `3.png` .

---

## Hidden Data Extraction

To extract hidden files, use **foremost**:

```bash
foremost 3.png
```

The result is an additional PNG file:

{{< image src="./images/00000102.png" >}}

However, when opened, this image also does not show information that is immediately readable visually.

---

## Completion with Steganography (XOR)

Refer to the instructions in the picture `3.png` Then the most reasonable approach is **bitwise operations between images**.

Steps taken:

1. Open **StegSolve**
2. Load `1.png` and `00000102.png` 3. Try various image operations

On the **XOR** operation, the success flag is displayed clearly:

{{< image src="./images/solved.png" >}}

---

##Flags

```text
CTFlearn{Santa_1s_C0ming}
```
