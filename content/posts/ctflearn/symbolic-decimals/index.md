---
title: "Symbolic Decimals"
date: 2026-02-22
categories:
  - Crypto
tags:
  - Crypto
  - CTFlearn
collections:
  - CTFlearn
---


{{< admonition tldr "TL;DR" >}}

**Symbolic Decimals** is a crypto challenge from **CTFlearn**. Did you know that you can hide messages with symbols? For example, `!@#$%^&*(` is `123456789!` Now Try: `^&,*$,&),!@#,*#,!!^,(&,!!$,(%,$^,(%,*&,(&,!!$,!!%,(%,$^,(%,&),!!!,!!$,(%,$^,(%,&^,!)%,!)@,!)!,!@%` However, this isn't as easy as you might think.

{{< /admonition >}}

## Challenge Description

Did you know that you can hide messages with symbols? For example, `!@#$%^&*(` is `123456789!` Now Try: `^&,*$,&),!@#,*#,!!^,(&,!!$,(%,$^,(%,*&,(&,!!$,!!%,(%,$^,(%,&),!!!,!!$,(%,$^,(%,&^,!)%,!)@,!)!,!@%` However, this isn't as easy as you might think.

---

## Technical Analysis

From the example given, it can be concluded that there is **mapping symbols to decimal digits** as follows:

```
 ! = 1
 @ = 2
 # = 3
 $ = 4
 % = 5
 ^ = 6
 & = 7
 * = 8
 ( = 9
 ) = 0
```

The ciphertext is separated by commas ( `,` ), which indicates that each group of symbols represents **ASCII code in decimal form**.

Thus, the decoding process is carried out in two stages:

1. Convert symbols to digits based on mapping.
2. Convert the resulting decimal digits to ASCII characters.

---

## Decode Process

To speed up and minimize errors, the decoding process is carried out using the following simple Python script:

```python
s = "^&,*$,&),!@#,*#,!!^,(&,!!$,(%,$^,(%,*&,(&,!!$,!!%,(%,$^,(%,&),!!!,!!$,(%,$^,(%,&^,!)%,!)@,!)!,!@%"

mapping = {
    '!': '1', '@': '2', '#': '3', '$': '4', '%': '5',
    '^': '6', '&': '7', '*': '8', '(': '9', ')': '0'
}

numeric = "".join(mapping.get(c, c) for c in s)

ascii_codes = numeric.split(',')
decoded = ''.join(chr(int(code)) for code in ascii_codes)

print(decoded)
```

### Script Explanation

* Each symbol is converted into a digit according to mapping.
* The result is a series of decimal numbers separated by commas.
* Each number is then converted to a character using ASCII conversion.

---

## Results

After running the script above, the output is obtained:

```
CTF{Star_._Wars_._For_._Life}
```

---

## Flags

```text
CTF{Star_._Wars_._For_._Life}
```
