---
title: "Suspicious Message"
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

**Suspicious Message** is a crypto challenge from **CTFlearn**. Hello! My friend Fari sent me this suspicious message: `___FL0X___` and `photo.png` .   Help me decrypt this!

{{< /admonition >}}

## Challenge Overview

Hello! My friend Fari sent me this suspicious message: `___FL1X___` and `photo.png` .  
Help me decrypt this!

---

## Technical Analysis

Given two main artifacts:

1. A **ciphertext**
2. A picture `photo.png` Upon opening `photo.png` , you can see the alphabet in the form of a **5x5 matrix**. This pattern is very typical and leads directly to the **Playfair Cipher**, a classic cipher that:

* Uses a 5x5 matrix
* Combining certain letters (generally I/J)
* Encrypt text per letter pair (digraph)

{{< image src="./images/photo.png" >}}

Based on the visual clues from the image, it can be concluded that:

* `photo.png` functions as **key square**
* Ciphertext is most likely encrypted using **Playfair Cipher**

---

## Decryption Process

To decrypt the message, the online tool [**Boxentriq – Playfair Cipher**](https://www.boxentriq.com/code-breaking/playfair-cipher) is used:

{{< image src="./images/decoded.png" >}}

The decryption results obtained:

```
CTFlearn{Pl4Yf41R_1S_C00l_c1PheRrRR}
```

---

## Flags

```text
CTFlearn{Pl4Yf41R_1S_C00l_c1PheRrRR}
```
