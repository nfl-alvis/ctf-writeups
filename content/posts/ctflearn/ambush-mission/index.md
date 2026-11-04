---
title: "Ambush Mission"
date: 2026-02-22
categories:
  - Misc
tags:
  - Misc
  - CTFlearn
collections:
  - CTFlearn
---



{{< admonition tldr "TL;DR" >}}

**Ambush Mission** is a misc challenge from **CTFlearn**. Hi, I can't tell you my name since now I'm on a mission. In case to arrest our fugitive target, our team had intercepted communication between the target and his fellow and found this image [clue.png](https://mega.nz/#!TKZ3DabY!BEUHD7VJvq_b-M22eD4VfHv_PPBnW2m7CZUfMbveZYw).   It looks like they are going to meet in a specific place, but we still don't know the time yet. Can you help me?

{{< /admonition >}}

## Challenge Description

Hi, I can't tell you my name since now I'm on a mission. In case to arrest our fugitive target, our team had intercepted communication between the target and his fellow and found this image [clue.png](https://mega.nz/#!TKZ3DabY!BEUHD7VJvq_b-M22eD4VfHv_PPBnW2m7CZUfMbveZYw).  
It looks like they are going to meet in a specific place, but we still don't know the time yet. Can you help me?

---

## Initial Analysis

The `clue.png` file is opened visually for initial inspection. No suspicious indications such as hidden text or unusual visual artifacts were found.

{{< image src="./images/clue.png" >}}

Because there are no visible clues, it is very likely that information is hidden using **steganography** techniques, especially at the bit level.

---

## Technical Analysis Steganography

To analyze possible hidden data, the **StegSolve** tool is used with a focus on examining the **bit plane**.

The analysis results show that there is a suspicious string on **Red Plane 0**:

```
==QTh9lMx8Fd08VZt9FdFNTb
```

{{< image src="./images/solved.bmp" >}}

---

## Decoding Process

The string has the typical **Base64** characteristics, but with the reverse character order. This can be recognized by the padding character `==` which should be at the end of the string, not at the beginning.

## Decoding methodology:

1. Reverse the string order (reverse).
2. Decode the result using Base64.

The process is carried out with the following command line:

```bash
echo '==QTh9lMx8Fd08VZt9FdFNTb' | rev | base64 -d
```

Output:

```
m3Et_me_4t_12_aM
```

---

##Flags

```text
m3Et_me_4t_12_aM
```
