---
title: "PikesPeak"
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

**PikesPeak** is a forensics challenge from **CTFlearn**. Pay attention to those strings! [PikesPeak.jpg](https://ctflearn.com/challenge/download/935)

{{< /admonition >}}

## Challenge Overview

Pay attention to those strings!
[PikesPeak.jpg](https://ctflearn.com/challenge/download/935)

---

## Technical Analysis

The `PikesPeak.jpg` file is opened visually and does not display visible indications of steganography.

{{< image src="./images/PikesPeak.jpg" >}}

Based on the hint challenge, the ASCII string embedded in the file is extracted using the command `strings` .

```bash
strings PikesPeak.jpg
```

From the result of executing the command, several candidate flags were found:

```
CTFLEARN{PikesPeak}
CTFLearn{Colorado}
%ctflearn{MountainMountainMountain}
\#cTfLeArN{CTFMountainCTFmOUNTAIN}
CTF{AsPEN.Vail}
CTFlearn{Gandalf}
ctflearning{AUCKLAND}
ctfLEARN{MtDoom}
6ctflearninglearning{Mordor.TongariroAlpineCrossing}
+CTFLEARN{MountGedePangrangoNationalPark}
$ctflearncTfLeARN{MountKosciuszko}
```

---

## Validate Flags

Based on the flag format standards on the **CTFlearn** platform, valid flags must follow the format:

```
CTFlearn{...}
```

Of all the candidates found, only one string **fully complies with the official flag format** with no additional characters or capitalization errors.

---

##Flags

```text
CTFlearn{Gandalf}
```
