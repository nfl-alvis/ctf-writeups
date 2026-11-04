# Qr Rusak 1



{{< admonition tldr "TL;DR" >}}

**broken qr 1** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description
Challenge provides a QR Code that is damaged at the corners, so the **finder pattern** is incomplete and the QR cannot be read by ordinary scanners.

The task is to fix the missing parts, then decode the QR to get the flag.

{{< image src="./images/qr1.png" >}}

---

## Solution Steps

### 1. Identify QR Damage
From the observation results, the damage was at the corner of the QR (finder pattern).  
The finder pattern is an important component of the QR Code, so if it is lost the QR cannot be recognized.

---

### 2. Added Pattern Finder
The main step is to add back the missing finder pattern so that the QR structure is valid again.
Used online tools, namely [photopea.com](https://www.photopea.com/)
The added finder pattern follows the standard QR pattern:

- big black box
- white box inside
- small black box in the middle

{{< image src="./images/finderPattern.png" >}}

---

### 3. Decoding QR
Once the finder pattern is restored, the QR is valid again and can be read using a QR decoder (eg zbarimg / regular scanner).

Decode result:

{{< image src="./images/solved.png" >}}

---

## Flags

```
FGTE{REDACTED}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/qr-rusak-1/  

