# Reverse & Binary (Cryptography) — Writeup



{{< admonition tldr "TL;DR" >}}

**Reverse & Binary (Cryptography) — Writeup** is a crypto challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

The challenge provides a ciphertext that looks like the encoding result, but the format is invalid because the padding character `=` is at the **beginning** of the string.

This indicates that the data has most likely been **reverse**.

Ciphertext:

```
=EDMxETMxEDMgATMxEDMwEDMgEDMxATMwEDMgATMxADMwEDMgETMxETMwEDMgEDMwETMwEDMgATMwATMwEDMgEDMwADMwEDMgATMxEDMwEDMgEDMwEDMwEDMgATMwADMwEDMgETMxETMwEDMgADMxADMwEDMgATMxEDMwEDMgEDMwADMwEDMgETMxETMwEDMgEDMxADMwEDMgETMwATMwEDMgATMwATMwEDMgEDMxADMwEDMgATMxATMwEDMgEDMxADMwEDMgATMwATMwEDMgETMwETMxEDMgEDMxADMwEDMgADMxATMwEDMgETMxADMwEDMgATMxADMwEDM
```

---

## Solution Steps

### 1. Reverse Ciphertext

Because normal Base64 always puts `=` at the end, the first step is to reverse the string first.

On CyberChef:
- **Reverse** (By Character)

Once reversed, the format becomes valid Base64.

---

### 2. Decode Base64

Once the sequence is correct, The next step is to open the first layer:

- **From Base64**

The result is a collection of binary numbers separated by spaces.

---

### 3. Decode Binary to ASCII

Since the output is binary, the final step is:

- **From Binary**
  - Delimiter: `Space` - Byte length: `8` The final result is a plaintext flag.

{{< image src="./images/solved.png" >}}

---

## Flags

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/reflected-bits/  

