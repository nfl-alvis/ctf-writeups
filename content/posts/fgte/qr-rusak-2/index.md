---
title: "qr rusak 2"
date: 2026-02-22
categories:
  - Misc
tags:
  - Misc
  - FGTE
collections:
  - FGTE
---


{{< admonition tldr "TL;DR" >}}

**broken qr 2** is a misc challenge from **FGTE**. The hint provided leads to the online tool:

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Challenge Description
The challenge provides a QR Code that looks damaged, especially in the **format information / finder pattern** area.  
QR cannot be scanned directly using ordinary tools, so it needs to be restored first.

The hint provided leads to the online tool:

- https://merri.cx/qrazybox/

The end goal is to recover the QR and get the flag.

---

## Solution Steps

### 1. Load QR into QrazyBox
Open the QrazyBox website, then:

1. Click **New Project**
2. Select **Load Existing Image**
3. Upload the QR image from the challenge

---

### 2. Bruteforce Format Information Pattern
Because the QR is damaged in the pattern area, QrazyBox provides a bruteforce feature to re-guess the format of the missing info.

Steps:

1. Click the **Tools** menu
2. Select the **Bruteforce Format Info Pattern** feature

---

### 3. Decode QR After Bruteforce
Once the bruteforce is done, enter decode mode:

1. Click the **Editor Mode** button on the left  
   (this will move the view from editor to decode mode)
2. Click **Decode**

Because you have previously run bruteforce, QrazyBox will automatically try all possible suitable pattern formats.

---

### 4. Decode Results (Base64 Corrupt)
From the decode result, we get a Base64 string but it is still slightly corrupted:
It can be seen that there is an is invalid character which is not part of the Base64 alphabet, meaning that there is 1 byte that is damaged in the QR decoding results.

{{< image src="./images/solved.png" >}}

---

### 5. Repair Broken Base64
The final solution is to replace the corrupted part with valid Base64 characters.

How:

- Replace broken characters with one of the Base64 characters ( `A–Z` , `a–z` , `0–9` , `+` , `/` )
- Re-decode until it produces plaintext that makes sense according to the flag format

After being repaired and decoded, the plaintext obtained is in the form of an FGTE flag.

---

## Flags

```
FGTE{REDACTED}
```
