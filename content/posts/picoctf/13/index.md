---
title: "picoCTF 2019 – Cryptography: 13"
date: 2026-03-23
categories:
  - Crypto
tags:
  - Crypto
  - picoCTF
collections:
  - picoCTF
---

{{< admonition tldr "TL;DR" >}}
**picoCTF 2019 – Cryptography: 13** is a crypto challenge from **picoCTF**. Cryptography can be easy, do you know what ROT13 is?
{{< /admonition >}}

## Description

Cryptography can be easy, do you know what ROT13 is?

```
cvpbPGS{abg_gbb_onq_bs_n_ceboyrz}
```

<details>
  <summary><h2>Hint</h2></summary>
  This can be solved online if you don't want to do it by hand!
</details>

---

## Objective

The objective of this challenge is to decode a string encrypted using the **ROT13 cipher** and recover the flag.

---

## Analysis

**ROT13 (Rotate by 13)** is a simple substitution cipher where:

* Each letter is shifted **13 positions** in the alphabet
* Applying ROT13 twice returns the original text

Example:

* `a ↔ n`
* `b ↔ o`
* `c ↔ p`

The given ciphertext:

```
cvpbPGS{abg_gbb_onq_bs_n_ceboyrz}
```

---

## Approach

We can decode the string using the `tr` command in Linux:

```bash
echo "cvpbPGS{abg_gbb_onq_bs_n_ceboyrz}" | tr 'A-MN-Za-mn-z' 'N-ZA-Mn-za-m'
```
