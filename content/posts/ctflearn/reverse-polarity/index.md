---
title: "Reverse Polarity"
date: 2026-02-22
categories:
  - Crypto
tags:
  - Crypto
  - CTFlearn
collections:
  - CTFlearn
---


{{< admonition tldr "TL;DR" >}}

**Reverse Polarity** is a crypto challenge from **CTFlearn**. I got a new hard drive just to hold my flag, but I'm afraid that it rotted. What do I do? The only thing I could get off of it was this: 0100001101010100010001100111101101000010011010010111010001011111 0100011001101100011010010111000001110000011010010110111001111101

{{< /admonition >}}

## Description

I got a new hard drive just to hold my flag, but I'm afraid that it rotted. What do I do? The only thing I could get off of it was this: 0100001101010100010001100111101101000010011010010111010001011111 0100011001101100011010010111000001110000011010010110111001111101

---

## Technical Analysis 

Given data in the form of a series of numbers `0` and `1` without separators, which indicates **8-bit ASCII** binary representation.
The data length is a multiple of 8, so it can be assumed that each 8 bits represents one ASCII character.

```
01000011010101000100011001111011010000100110100101110100010111110100011001101100011010010111000001110000011010010110111001111101
```

---

## Decode Process

The decoding process is carried out by converting the _binary string_ directly to ASCII characters using the `perl` utility.

```bash
echo 01000011010101000100011001111011010000100110100101110100010111110100011001101100011010010111000001110000011010010110111001111101 | perl -lpe '$_=pack("B*",$_)'
```

Output:

```
CTF{Bit_Flippin}
```

---

## Flags

```CTF{Bit_Flippin}```
