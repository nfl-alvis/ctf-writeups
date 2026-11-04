# QR Code



{{< admonition tldr "TL;DR" >}}

**QR Code** is a misc challenge from **CTFlearn**. ---

{{< /admonition >}}

---

## Description

Do you remember something known as QR Code? Simple. Here for you : [link](https://mega.nz/#!eGYlFa5Z!8mbiqg3kosk93qJCP-DBxIilHH2rf7iIVY-kpwyrx-0)

---

## Technical Analysis

Provided in the form of an image **_QR Code_**. The initial step taken is to carry out the **_QR Code_** decoding process to extract the raw data stored in it.

{{< image src="./images/qrcode.39907201.png" >}}

The decoding process **_QR Code_** is carried out using [QRazyBox](https://merri.cx/qrazybox/), which produces an encrypted string in Base64 format:

{{< image src="./images/Decoded.png" >}}

```
c3ludCB2ZiA6IGEwX29icWxfczBldHJnX2RlX3BicXI=
```

---

## Base64 decode

The Base64 string is then decoded using the CLI utility to obtain the initial _plaintext_:

```
echo "c3ludCB2ZiA6IGEwX29icWxfczBldHJnX2RlX3BicXI=" | base64 -d
```

Output:

```
synt vf : a0_obql_s0etrg_de_pbqr
```

The output is still not directly meaningful and looks like the result of a _substitution cipher_.

---

## Technical Analysis Ciphers

The character pattern in the previous results indicates the use of the **ROT13 cipher**, which is a simple substitution method that shifts letters by 13 positions.
**ROT13** decoding is performed using [dCode.fr](https://www.dcode.fr/rot-13-cipher), resulting in the following plaintext:

{{< image src="./images/DecodeRot13.png" >}}

```flag is : n0_body_f0rget_qr_code```

---

## Flags

```n0_body_f0rget_qr_code```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/qr-code/  

