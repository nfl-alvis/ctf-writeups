---
title: "Blank Page"
date: 2026-02-22
categories:
  - Forensics
tags:
  - Forensics
  - CTFlearn
collections:
  - CTFlearn
---


{{< admonition tldr "TL;DR" >}}

**Blank Page** is a forensics challenge from **CTFlearn**. I've just graduated the Super Agent School. This is my first day as a spy. The Master-Mind sent me the secret message, but I don't remember how to read this. Help!   [TheMessage.txt](TheMessage.txt)

{{< /admonition >}}

##Challenge

I've just graduated the Super Agent School. This is my first day as a spy. The Master-Mind sent me the secret message, but I don't remember how to read this. Help!  
[TheMessage.txt](TheMessage.txt)

---

## Analysis

We are provided with a text file named `TheMessage.txt` . When the file is read using the standard command like:

```bash
cat TheMessage.txt
```

no output is displayed, as if the file is empty. This indicates the possible use of **invisible characters**.

To investigate further, the file contents were analyzed at the byte level using `xxd` :

```bash
xxd TheMessage.txt | head -20
```

The results show repeating byte patterns, specifically:

```
20 e2 80 8f
```

* `0x20` → ASCII space character
* `0xE2 0x80 0x8F` → **Unicode Right-to-Left Mark (RLM)**, invisible character

From this pattern it can be concluded that the message is hidden using **two different types of characters but both visually invisible**, which are commonly used to encode binary data.

### Encoding Assumptions

* Space ( `0x20` ) is represented as **0**
* Invisible Unicode characters ( `0xE2808F` ) are represented as **1**

With this assumption, the entire contents of the file can be extracted into a bitstream, then grouped by 8 bits (1 byte) and converted to ASCII characters.

---

## Decoding

To perform extraction and decoding, the following Python script is used:

```python
data = open("TheMessage.txt", "r").read()

bits = ""
for char in data:
    if ord(char) == 32:
        bits += "0"
    else:
        bits += "1"

decoded = ""
for i in range(0, len(bits), 8):
    byte = bits[i:i+8]
    if len(byte) == 8:
        decoded += chr(int(byte, 2))

print("=== BITSTREAM ===")
print(bits)
print("\n=== DECODED TEXT ===")
print(decoded)
```

---

## Results

Running the script produces the following decrypted text:

```
From The Global Anti-Terrorists Tactics

If you read this you passed. Congrats.
Your first task will come tomorrow.
Good luck.

CTFlearn{If_y0u_r3/\d_thi5_you_pa553d}
```

The hidden message was completely reconstructed, and a flag was found at the end of the message.

---

## Flags

```text
CTFlearn{If_y0u_r3/\d_thi5_you_pa553d}
```
