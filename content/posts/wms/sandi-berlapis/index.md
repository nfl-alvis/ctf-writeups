---
title: "Sandi Berlapis"
date: 2026-04-08
categories:
  - Crypto
tags:
  - Multi-stage Encoding
  - XOR
  - Base64
  - Hex
  - Reverse Engineering
collections:
  - WMS
---

> **A multi-layered cryptographic pipeline was used to obscure a confidential message. Can you reverse the transformations in the exact inverse order to recover the flag?**

{{< admonition tldr "TL;DR" >}}
**Sandi Berlapis** is a cryptography challenge featuring a four-stage sequential encoding pipeline: XOR with a repeating key (`PH4NT0M`), byte reversal, Base64 encoding, and Hex encoding. Because all four transformations are deterministic and completely reversible, inverting the operations in reverse order (`Hex Decode` $\rightarrow$ `Base64 Decode` $\rightarrow$ `Byte Reversal` $\rightarrow$ `XOR`) recovers the original flag.
{{< /admonition >}}

## Encoder Mechanism Analysis

The challenge provides the encryption script `encoder.py`:

```python
import base64

_K = bytes([0x50, 0x48, 0x34, 0x4E, 0x54, 0x30, 0x4D])  # b"PH4NT0M"


def phase_alpha(data: bytes, key: bytes) -> bytes:
    return bytes([b ^ key[i % len(key)] for i, b in enumerate(data)])


def phase_beta(data: bytes) -> bytes:
    return data[::-1]


def phase_gamma(data: bytes) -> bytes:
    return base64.b64encode(data)


def phase_delta(data: bytes) -> str:
    return data.hex()
```

### Transformation Pipeline

1. **Phase Alpha (XOR):** Plaintext bytes are XORed with key `_K` = `b"PH4NT0M"`.
2. **Phase Beta (Reversal):** The resulting byte sequence is reversed end-to-end (`data[::-1]`).
3. **Phase Gamma (Base64):** The reversed bytes are encoded using standard Base64.
4. **Phase Delta (Hex):** The Base64 ASCII bytes are converted into a hexadecimal representation.

We are given the ciphertext in `encoded_message.txt`:

```text
4d454d6d665541385a4342764a6e31514f6d4153567a702f5548677a49774d4c4b676336597a51454f44566e4251633d
```

---

## Decryption Strategy

Because symmetric XOR and encoding layers are inherently bi-directional, decryption simply executes the inverse functions in reverse sequence:

$$\text{Plaintext} = \text{XOR}\Big(\text{Reverse}\big(\text{Base64Decode}(\text{HexDecode}(C))\big), K\Big)$$

1. **Step 1:** Decode from Hex to raw Base64 bytes.
2. **Step 2:** Decode the Base64 bytes to raw ciphertext.
3. **Step 3:** Reverse the byte sequence (`data[::-1]`).
4. **Step 4:** XOR each byte with the repeating key `b"PH4NT0M"`.

---

## Automated Solver

We construct a Python solver script:

```python
#!/usr/bin/env python3
import base64

CIPHERTEXT_HEX = "4d454d6d665541385a4342764a6e31514f6d4153567a702f5548677a49774d4c4b676336597a51454f44566e4251633d"
KEY = b"PH4NT0M"

# Step 1: Hex decode
b64_bytes = bytes.fromhex(CIPHERTEXT_HEX)

# Step 2: Base64 decode
reversed_bytes = base64.b64decode(b64_bytes)

# Step 3: Reverse bytes
xored_bytes = reversed_bytes[::-1]

# Step 4: XOR with repeating key
plaintext = bytes([b ^ KEY[i % len(KEY)] for i, b in enumerate(xored_bytes)])

print("[+] Decrypted Flag:", plaintext.decode())
```

Running the script outputs:

```text
[+] Decrypted Flag: WMS{l4y3r3d_3nc0d1ng_0rd3r_m4tt3rs}
```

---

## Flag

```text
WMS{l4y3r3d_3nc0d1ng_0rd3r_m4tt3rs}
```

---

## Security Takeaway

Encoding schemes (such as Hex and Base64) provide data representation, not cryptographic secrecy. Furthermore, stacking reversible encodings with a static XOR key offers security through obscurity, which can be immediately dismantled once the transformation sequence is identified.
