---
title: "Kunci Sungai Nil"
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

**Locks of the Nile** is a crypto challenge from **FGTE**. Given two files:

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Description Challenge

Given two files:

* `clue.png` * `inskripsi_tersembunyi.txt` The description states that the artifact contains a mixture of Greek script and Egyptian Hieroglyphic symbols, as well as a clue:

> "The name etched on the tablet, spoken by the priests during the inundation, unlocks the cryptic symbols. This name must be continuously repeated over the coded message."

Goal: find hidden messages by connecting hieroglyphs and encrypted text.

---

## 1. Key Extraction from Hieroglyphs

The `clue.png` file contains Hieroglyphic symbols in Manuel de Codage format.

The symbols are decoded using tools like dcode.fr and produce a string:

```
nirAhiis
```

{{< image src="./images/hieroglyphsDcode.png" >}}

The instructions state that the name should be “continuously repeated over the coded message”, which cryptographically refers to a cipher with repeated keys, namely the **Vigenère cipher**.

Thus, `nirAhiis` is identified as the encryption key.

---

## 2. Ciphertext Analysis

Contents of file `inskripsi_tersembunyi.txt` :

```
zqPOn86azpV7zpHOoM6cX0pWRFZSX86fV1RWRl/OklnOlV/Ooc6czpN9
```

The character structure ( `A–Z` , `a–z` , `0–9` , `+` , `/` ) indicates that the string is **Base64**.

---

## 3. Base64 decoding

After decoding Base64, we get:

```
ΣΟΚΕ{ΑΠΜ_JVDVR_ΟWTVF_ΒYΕ_ΡΜΓ}
```

These results show a mixture of Greek and Latin letters.

---

## 4. Transliteration Greek to Latin

The Greek letters that appear are transliterated into the Latin alphabet:

* Σ → S
* Ο → O
* Κ → K
* Ε → E
* Α → A
* Π → P
* Μ → M
*Β → B
* Ρ → R
* Γ → G

So the text becomes:

```
SOKE{APM_JVDVR_OWTVF_BYE_RMG}
```

This structure resembles the CTF flag format, but is still in encrypted form.

---

## 5. Decryption with Vigenère Cipher

Using keys:

```
nirAhiis
```

Vigenère's decryption of the text is carried out:

```
SOKE{APM_JVDVR_OWTVF_BYE_RMG}
```

Decryption results:

{{< image src="./images/solved.png" >}}

---

##Flags

```
FGTE{Redacted}
```
