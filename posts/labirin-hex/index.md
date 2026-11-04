# Labirin Hex



{{< admonition tldr "TL;DR" >}}

**Hex Maze** is a crypto challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

We are provided with a file `labirin_hex.txt` which contains a series of long **hexadecimal** strings with many layers of encoding.

The objective of this challenge is to find a **key** , which will later be used as an **XOR** key to obtain the flag.

---

## Technical Analysis and Solution

### 1. Layered Hex Decoding

The first step is to try to decode the contents of the file from hex format.

After performing **decode hex 7 times** using CyberChef, the results obtained are:

{{< image src="./images/dcodeHex.png" >}}

```
a2VsdWFyX2RhcmlfbGFiaXJpbg== adalah kunci untuk keluar dari labirin ini
```

The interesting part is the string:

```
a2VsdWFyX2RhcmlfbGFiaXJpbg==
```

The string is clearly **Base64**.

---

### 2. Decode Base64

Decoding Base64 results in:

```
keluar_dari_labirin
```

Based on the previous sentence:

> “...is the key to getting out of this maze”

It can be concluded that:

```
keluar_dari_labirin
```

is the **XOR key** that must be used.

---

### 3. Search for Relevant Ciphertext

When the number of hex decodes is reduced to **6 times**, a strange string appears that looks suspicious:

{{< image src="./images/acak.png" >}}

```
_SLAVRjPAF\[B_IXZEZGADfDV@IiUATR\KS_UV@SXR^oLVVIAYN^LFPRhWAGZWB^GIXE^DAE;
```

The string contains many non-printable characters and strange symbols, which are usually an indication of an XOR result with an unknown key.

If explored further (with 5 hex decodes), it is found that the string comes from the following hex representation:

{{< image src="./images/acakHex.png" >}}

```
5f 53 4c 41 56 52 6a 50 41 46 5c 7f 5b 03 42 5f 11 49 58 5a 45 5a 47 41 44 66 44 56 40 49 69 55 41 54 0c 52 5c 08 4b 53 5f 55 56 40 7f 53 58 52 5e 6f 4c 56 56 49 41 59 4e 5e 03 4c 46 50 52 68 57 41 47 0f 7f 5a 57 42 5e 47 49 58 0e 45 5e 44 41 45 3b
```

This is a very strong indication as **ciphertext XOR**.

---

### 4. Perform XOR with Key

Previously discovered keys:

```
keluar_dari_labirin
```

Then an XOR is carried out between the ciphertext and the key.

The XOR results in the following hex representation:

{{< image src="./images/hasilXor.png" >}}

```
46 47 54 45 7b 6c 61 62 69 72 69 6e 5f 63 72 79 70 74 30 5f 31 73 5f 66 75 6e 21 7d
```

---

### 5. Decode Hex to ASCII

Performing the final hex decode results in:

{{< image src="./images/solved.png" >}}

---

## Flags

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/labirin-hex/  

