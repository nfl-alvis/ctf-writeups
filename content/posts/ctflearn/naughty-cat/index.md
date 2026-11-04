---
title: "Naughty Cat — Writeup Forensik (CTF)"
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

**Naughty Cat — Writeup Forensics (CTF)** is a forensics challenge from **CTFlearn**. I think my cat is hiding something... [cut3_c4t.png](https://ctflearn.com/challenge/download/890)

{{< /admonition >}}

## Challenge Description

I think my cat is hiding something...  
[cut3_c4t.png](https://ctflearn.com/challenge/download/890)

---

## Initial Analysis

The file `cut3_c4t.png` when opened normally does not show any visual irregularities. There are no visible artifacts, hidden messages or indications of steganography.

{{< image src="./images/cut3_c4t.png" >}}

Since it is visually clean, the next step is to do a file structure analysis.

---

## Hidden Data Detection

To check for possible hidden data, use the **zsteg** tool:

```bash
zsteg cut3_c4t.png
```

The analysis results show that there is **additional data after the IEND chunk**, which indicates the file is compromised by other data:

```
[?] 75618 bytes of extra data after image end (IEND)
extradata:0 .. file: RAR archive data, v5
```

Initial conclusion:
**There is a RAR archive embedded in the PNG file.**

---

## Embedded File Extraction

To extract this data, **binwalk** is used:

```bash
binwalk -e cut3_c4t.png
```

The extraction results in two important files:

```
purrr_2.mp3
y0u_4r3_cl0s3.rar
```

---

## Technical Analysis of RAR Files

When trying to extract the RAR archive, an error appears:

```
y0u_4r3_cl0s3.rar is not RAR archive
```

This was suspicious, so an inspection was performed signature file using `xxd` :

```bash
xxd y0u_4r3_cl0s3.rar
```

The initial byte of the file shows:

```
43 61 74 21  -> "Cat!"
```

Even though the RAR file signature should be:

```
52 61 72 21  -> "Rar!"
```

**Signature file intentionally modified to fool detection.**

---

## Signature File Repair

Signature is fixed by overwriting the first 4 bytes of the file:

```bash
printf '\x52\x61\x72\x21' | dd of=y0u_4r3_cl0s3.rar bs=1 seek=0 count=4 conv=notrunc
```

Re-verify:

```bash
file y0u_4r3_cl0s3.rar
```

Results:

```
RAR archive data, v5
```

The file is now valid.

---

## RAR Archive Extraction

When extracted, the archive asks for a password:

```bash
unrar x y0u_4r3_cl0s3.rar
```

```
Enter password for f1n4lly.txt:
```

The password is not yet known, so the analysis continues with the audio file.

---

## Technical Analysis Audio (Spectrogram)

The `purrr_2.mp3` file was analyzed using **Sonic Visualiser** with **spectrogram** mode.

{{< image src="./images/Spectogram.png" >}}

In the frequency representation, a hidden string is clearly visible:

```
sp3ctrum_1s_y0ur_fr13nd
```

This technique utilizes message embedding in the audio frequency domain.

---

## Decrypt RAR Archive

The string of spectrogram analysis results is used as a password:

```bash
unrar x y0u_4r3_cl0s3.rar
```

Extraction is successful and produces files:

```
f1n4lly.txt
```

---

## Final Technical Analysis File

Contents of file `f1n4lly.txt` :

```
            __/|
            \o.O|
       _____(___)______
      |       U        |________    __
      |ZjByM241MWNzX21h|        |__|  |_________
      |________________|NXQzcg==|::|  |        /
      |                \._______|::|__|       <
      |                         \::/  \._______\
      |
      |
```

The string is identified as **Base64**.

Decoding is done by:

```bash
echo 'ZjByM241MWNzX21hNXQzcg==' | base64 -d
```

Decoding result:

```
f0r3n51cs_ma5t3r
```

---

## Flags

```
f0r3n51cs_ma5t3r
```
