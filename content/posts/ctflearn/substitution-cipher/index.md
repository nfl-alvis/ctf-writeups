---
title: "Substitution Cipher"
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

**Substitution Cipher** is a crypto challenge from **CTFlearn**. ---

{{< /admonition >}}

---

##Challenge

Someone gave me this, but I haven't the slightest idea as to what it says!
[Substitution.txt](https://mega.nz/#!iCBz2IIL!B7292dJSx1PGXoWhd9oFLk2g0NFqGApBaItI_2Gsp9w)  
Figure it out for me, will ya?

---

## Technical Analysis

We are provided with the file `Subsititution.txt` which is the text encrypted with **Substitution Cipher**.

---

## Decrypt

Decrypting the message on the [planetcalc.com](https://planetcalc.com/8047/) page:

{{< image src="./images/decrypted.png" >}}

By using automatic frequency analysis, the plaintext was completely reconstructed.

---

##Flags

```IFONLYMODERNCRYPTOWASLIKETHIS```
