---
title: "Base Maze"
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

**Base Maze** is a crypto challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

This challenge provides a long ciphertext containing random symbols, numbers and letters.

Main clue:

> "Enter the base maze — one by one the layers must be passed.  
> Be careful: order matters. The flag is after the last layer."

From this clue, we can conclude that the ciphertext has gone through **a lot of "Base-X" based encoding**, and we have to open the layers **in the right order**.

---

## Solution Steps

### 1. Identify patterns

Ciphertext contains many non-alphabetic characters (symbols), so it is likely that the final encoding will be a base that supports many characters such as:

- Base85
- Base92

Because the clue mentions a "base maze", the best step is to try decoding in layers from the largest base to the smallest base.

---

### 2. Decode the last layer (Base92)

In CyberChef use:

- **From Base92**

The result is still not plaintext, but changed to a format suitable for the next base.

---

### 3. Decode Base85

Add operation:

- **From Base85**

Since Base85 uses a more complex symbol alphabet, make sure the **Remove non-alphabet chars** option is active.

---

### 4. Decode Base64

Add operation:

- **From Base64**

The output starts to look “tidier” and closer to the smaller base format.

---

### 5. Decode Base62

Add operation:

- **From Base62**

Base62 only uses characters:

- `0-9` - `A-Z` - `a-z` ---

### 6. Decode Base58

Add operation:

- **From Base58**

Base58 is commonly used in crypto and eliminates ambiguous characters like `0 O I l` .

---

### 7. Decode Base45

Add operation:

- **From Base45**

Base45 usually appears in modern QR / encodings (example: EU Digital Certificate).

---

### 8. Decode Base32 (last layer)

Add operation:

- **From Base32**

At this stage the plaintext flag finally appears.

---

## Results

After the entire decoding sequence is executed according to the clue:

**Base92 → Base85 → Base64 → Base62 → Base58 → Base45 → Base32**

Obtained flags:

{{< image src="./images/solved.png" >}}

---

## Flags

```
FGTE{Redacted}
```
