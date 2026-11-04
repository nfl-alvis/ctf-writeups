---
title: "Sinyal Rahasia 1944"
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

**Secret Signal 1944** is a misc challenge from **FGTE**. The final goal is to find the destination address (URL) hidden in the message.

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Challenge Description
Challenge provided a short ciphertext claiming to be a 1944 radio message from an enemy U-Boat unit.  
Also given is a 3×3 number matrix which is a daily configuration guide for decrypting messages.

The final goal is to find the destination address (URL) hidden in the message.

---

## Solution Steps

### 1. Identify Cipher from Hint
The hint given is an emoji:

```
🥨
```

The emoji is so synonymous with **Germany**, that the context of “1944 radio”, “daily configuration”, and “U-Boat” immediately leads to a classic cipher:

**Enigma Machine**

The 3×3 matrix provided is thought to function as a configuration guide.

---

### 2. Numerical Matrix Interpretation
Given matrix:

```
1 2 3
4 5 6
7 8 9
```

After the configuration is applied to the Enigma decoding process (according to the challenge), the ciphertext:

```
yucmn dytgt kyctx mudhn xkzec gmrfd xkztl spge
```

successfully decoded into string chunks:

{{< image src="./images/decodeEnigma.png" >}}

```
https nazis ignal enigm asecr eturl verce lapp
```

---

### 3. Reconstruct Into a Valid URL
The resulting plaintext is still split by 5 characters, so it needs to be combined again.

If compiled into a valid URL:

[https://nazisignalenigmasecreturl.vercel.app/](https://nazisignalenigmasecreturl.vercel.app/)

---

### 4. Website Investigation
After the URL is opened, the page does not immediately display a flag, but there is something suspicious about the favicon.

It turns out that the favicon is not an ordinary icon, but a **QR Code** image.

---

### 5. Download QR from the Website
QR can be retrieved directly using `wget` from the assets used by the website:

```bash
wget 'https://nazisignalenigmasecreturl.vercel.app/assets/qr-DXX1lDA7.png' -O favicon.png
```
---

### 6. Decode QR Code

The QR is then decoded using `zbarimg` :

```bash
zbarimg favicon.png
```

Output:

{{< image src="./images/solved.png" >}}
