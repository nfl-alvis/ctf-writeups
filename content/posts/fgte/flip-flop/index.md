---
title: "Flip-Flop"
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

**Flip-Flop** is a crypto challenge from **FGTE**. Challenge provides files:

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Challenge Description

Challenge provides files:

- `cipher.txt` (contains ciphertext in bytes / non-printable characters)
- `chall.py` (encryption script)

From `chall.py` , it is known that ciphertext is created by processing each plaintext character into 8-bit binary, then **reversing the bit order** (bit-reversal).

---

## Solution Steps

### 1. Encryption Algorithm Analysis

The main contents of the `encode()` function:

```python
bits = f"{ord(c):08b}"   # ubah char ke biner 8-bit
rev = bits[::-1]         # balik urutan bit
out.append(chr(int(rev, 2)))
```
Meaning:

* each character → 8-bit binary
* bits reversed (example `01000001` → `10000010` )
* the result is converted back to bytes/char

Because the bit-reversal operation is **involutive** (flipped twice back to its initial form), the decode function is exactly the same as the encode.

---

### 2. Create a Decryption Script

To return plaintext:

1. Read `cipher.txt` in binary mode
2. Decode using `latin-1` so that bytes 0–255 are not damaged
3. Run the same bit-reversal process

Scripts:

```python
def decode(text: str) -> str:
    out = []
    for c in text:
        bits = f"{ord(c):08b}"
        rev = bits[::-1]
        out.append(chr(int(rev, 2)))
    return "".join(out)

if __name__ == "__main__":
    with open("cipher.txt", "rb") as f:
        cipher = f.read().decode("latin-1")

    plain = decode(cipher)
    print(plain)
```

---

### 3. Run Solver

```bash
python3 decode.py
```

{{< image src="./images/solved.png" >}}

---

## Flags

```
FGTE{Redacted}
```
