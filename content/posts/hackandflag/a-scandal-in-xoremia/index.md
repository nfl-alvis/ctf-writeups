---
title: "CTF Write-Up: Repeating-Key XOR & Known Plaintext Attack"
date: 2026-05-03
categories:
  - Crypto
tags:
  - Crypto
  - HackAndFlag
collections:
  - HackAndFlag
---



{{< admonition tldr "TL;DR" >}}

**CTF Write-Up: Repeating-Key XOR & Known Plaintext Attack** is a crypto challenge from **HackAndFlag**. The following is a professional *write-up* format that you can immediately use for CTF documentation or reporting.

{{< /admonition >}}

The following is a professional *write-up* format that you can immediately use for CTF documentation or reporting. 

***

# CTF Write-Up: Repeating-Key XOR & Known Plaintext Attack

## 📌 Challenge Summary
This cryptography challenge involves decrypting a *ciphertext* using the *Known Plaintext Attack* (KPA) method against the *XOR cipher* algorithm. By using pieces of text whose position is known (*known plaintext*), we can extract the *key* which is used to decrypt the entire file and get the *flag*.

## 🛠️ Methodology & Completion Steps

### 1. Analysis and Known Plaintext Attack (KPA)
Based on initial analysis of the file structure, it is assumed that the document file begins with the opening sentence of the story *A Scandal in Bohemia*:
> `"To Sherlock Holmes she is always the woman."` The basic nature of the XOR operation is reversible ( `Ciphertext ⊕ Plaintext = Key` ). Since the length of this *known plaintext* is 43 characters, we can take the first 43 bytes of the *ciphertext* (in hexadecimal format) to XOR it:

```text
1d 2e 69 12 21 24 3b 2d 26 22 22 61 01 2e 25 2c 2c 32 69 32 21 24 69 28 3a 61 28 2d 3e 20 30 32 69 35 21 24 69 36 26 2c 28 2f 67
```

By performing an XOR operation between the *ciphertext* and the *known plaintext*, we get a *key stream* result in the form of: `"IAIAIAIAIAIAIAIAIAIAIAIAIAIAIAIAIAIAIAIAIAI"` ### 2. Identify Keys
From the XOR results in the first step, it is clear that there is a character pattern that continues to repeat itself constantly. This confirms that the algorithm used is a *repeating-key XOR cipher* with a *key* length of 2 bytes.

So, the main *key* used for encryption is: ** `"IA"` **

### 3. Entire File Decryption and Flag Extraction
After the *key* ( `"IA"` ) has been identified with certainty, the final step is to apply the XOR operation using the *key* to the entire *ciphertext* remaining in the file. This decryption process returns the original text intact. 

After reading it in its entirety, the *flag* is hidden and can be found on the very last line of the From the result decryption.

---

## 💻 Proof of Concept (PoC) Script
To replicate this *Known Plaintext Attack* process and decrypt it automatically, here is a Python *script* using *library* `pwntools` :

```python
from pwn import xor

# 1. Setup Data KPA
enc_hex = "1d 2e 69 12 21 24 3b 2d 26 22 22 61 01 2e 25 2c 2c 32 69 32 21 24 69 28 3a 61 28 2d 3e 20 30 32 69 35 21 24 69 36 26 2c 28 2f 67"
enc_bytes = bytes.fromhex(enc_hex)
known_plaintext = b"To Sherlock Holmes she is always the woman."

# 2. Ekstraksi Key Stream
extracted_key_stream = xor(enc_bytes, known_plaintext)
print(f"[*] Extracted Key Stream: {extracted_key_stream.decode()}")

# 3. Dekripsi File (Asumsi file dibaca ke dalam variabel 'full_enc_data')
key = b"IA"
print(f"[*] Actual Key Identified: {key.decode()}")

# full_enc_data = open('encrypted_file.txt', 'rb').read()
# decrypted_data = xor(full_enc_data, key)
# print(f"[*] Flag found: {decrypted_data.split(b'\n')[-1].decode()}")
```
