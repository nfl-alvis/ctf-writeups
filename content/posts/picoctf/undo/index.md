---
title: "picoCTF 2026 – Undo"
date: 2026-03-11
categories:
  - Misc
tags:
  - Misc
  - picoCTF
collections:
  - picoCTF
---

{{< admonition tldr "TL;DR" >}}
**picoCTF 2026 – Undo** is a misc challenge from **picoCTF**. This challenge presents an interactive service where the flag has been transformed multiple times using common Linux text-processing techniques.
{{< /admonition >}}

## Description

This challenge presents an interactive service where the flag has been transformed multiple times using common Linux text-processing techniques.

Our objective is to **reverse each transformation step-by-step** by entering the correct Linux command.

Connect to the service:

```
nc foggy-cliff.picoctf.net 53551
```

At each stage, the service shows:

* The **current transformed flag**
* A **hint describing the transformation**
* A prompt asking for the Linux command to reverse it.

---

## Challenge Walkthrough

### Step 1 – Base64 Decoding

Current string:

```
KXA2OTgxcHBxLWZhMDFnQHplMHNmYTRlRy1nazNnLXRhMWZlcmlyRShTR1BicHZj
```

Hint:

```
Base64 encoded the string.
```

To decode Base64:

```
base64 -d
```

---

### Step 2 – Reverse Text

Current string:

```
)p6981ppq-fa01g@ze0sfa4eG-gk3g-ta1ferirE(SGPbpvc
```

Hint:

```
Reversed the text.
```

To reverse a string in Linux:

```
rev
```

---

### Step 3 – Replace Dashes with Underscores

Current string:

```
cvpbPGS(Eriref1at-g3kg-Ge4afs0ez@g10af-qpp1896p)
```

Hint:

```
Replaced underscores with dashes.
```

To convert `-` back to `_`:

```
tr '-' '_'
```

---

### Step 4 – Replace Parentheses with Curly Braces

Current string:

```
cvpbPGS(Eriref1at_g3kg_Ge4afs0ez@g10af_qpp1896p)
```

Hint:

```
Replaced curly braces with parentheses.
```

To restore the original braces:

```
tr '()' '{}'
```

---

### Step 5 – ROT13 Cipher

Current string:

```
cvpbPGS{Eriref1at_g3kg_Ge4afs0ez@g10af_qpp1896p}
```

Hint:

```
Applied ROT13 to letters.
```

ROT13 can be reversed using the same transformation:

```
tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

---

## Final Result

After reversing all transformations, the original flag is revealed:

```
picoCTF{Revers1ng_t3xt_Tr4nsf0rm@t10ns_dcc1896c}
```

---

## Key Takeaways

This challenge demonstrates several common Linux text-processing techniques:

* `base64 -d` for Base64 decoding
* `rev` for reversing text
* `tr` for character substitution
* ROT13 using `tr 'A-Za-z' 'N-ZA-Mn-za-m'`

Understanding these commands is useful when dealing with **encoded or transformed text in CTF challenges**.
