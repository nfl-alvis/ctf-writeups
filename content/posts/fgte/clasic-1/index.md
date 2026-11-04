---
title: "clasic 1"
date: 2026-02-22
categories:
  - Crypto
tags:
  - Crypto
  - FGTE
collections:
  - FGTE
---


{{< admonition tldr "TL;DR" >}}

**classic 1** is a crypto challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

Given a long ciphertext that looks like Base64, the problem also provides an important hint:

* `70 -> 0` * `71 -> 1` This means that in the final layer we will deal with binary data disguised in the form of ASCII numbers.

---

## Solution Steps

### 1. Decode Base64

First we decode the ciphertext from Base64.

```bash
cat clasic1.txt | base64 -d
```

The output still looks like random text, but the shape has changed and there is a character pattern that resembles Base32.

---

### 2. Decode Base32

Because the Base64 result looks like the Base32 alphabet, we continue to decode Base32.

```bash
cat clasic1.txt | base64 -d | base32 -d
```

The result is a series of numbers like the following:

```
70|71|73|70|73|70|.. .
```

---

### 3. Take only values 70 and 71

From the hint, only the numbers 70 and 71 are important:

* `70` represents the `0` bit 
* `71` represents the `1` bit 

Meanwhile, other numbers such as `72` and `73` are just noise (distraction).

So the steps:

1. Remove all `|` characters 
2. Discard all numbers `72` and `73` 3. Replace:

   * `70` → `0` * `71` → `1` ---

### 4. Decode the binary results into plaintext

After changing it into a series of `0` and `1` bits, we decode it as binary (8-bits per byte).

In CyberChef you can use the following steps:

* **Find/Replace** for number → bit mapping
* **From Binary** (byte length 8)

After decoding, a flag appears.

{{< image src="./images/solved.png" >}}

---

## Flags

```
FGTE{Redacted}
```
