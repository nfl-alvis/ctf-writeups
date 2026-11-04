---
title: "The Simpsons"
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

**The Simpsons** is a crypto challenge from **CTFlearn**. Ya know, I was thinking... wouldn't the Simpsons use octal as a base system? They have 8 fingers... Oh, right! The problem! Ummm, something seems odd about this image... [ItsKrumpingTime.jpg](https://mega.nz/#!yfp1nYrQ!LOz_eucuKkjAaDqVvz3GWgbfdKWn8BhussKZbx6bUMg)

{{< /admonition >}}

Ya know, I was thinking... wouldn't the Simpsons use octal as a base system? They have 8 fingers... Oh, right! The problem! Ummm, something seems odd about this image...  
[ItsKrumpingTime.jpg](https://mega.nz/#!yfp1nYrQ!LOz_eucuKkjAaDqVvz3GWgbfdKWn8BhussKZbx6bUMg)

---

## Challenge Description

We are provided with an image file `ItsKrumpingTime.jpg` .
When analyzed with `strings` , Python code snippets and hints were found:

```
Ahh! Realistically the Simpsons would use octal instead of decimal!
```

This means that all numbers given are in **base 8 (octal)**.

---

## Enumeration

Running:

```bash
strings ItsKrumpingTime.jpg | tail
```

Obtained:

```python
encoded = 152 162 152 145 162 167 150 172 153 162 145 170 141 162
key = chr(SolutionToDis(110 157 167 040 155 165 143 150 040 144 151 144 040 115 141 147 147 151 145 040 157 162 151 147 151 156 141 154 154 171 040 143 157 163 164 077 040 050 104 151 166 151 144 145 144 040 142 171 040 070 054 040 164 157 040 164 150 145 040 156 145 141 162 145 163 164 040 151 156 164 145 147 145 162 054 040 141 156 144 040 164 150 145 156 040 160 154 165 163 040 146 157 165 162 051))
key = key + key + chr(ord(key)-4)
print(DecodeDat(key=key,text=encoded))
```

It can be seen that:

* There is data `encoded` * There is a mysterious function `SolutionToDis` * There is a hint about **octal**

---

## Decode SolutionToDis

Series of numbers:

```
110 157 167 040 155 165 143 150 040 144 151 144 040 115 141 147 147 151 145 040 157 162 151 147 151 156 141 154 154 171 040 143 157 163 164 077 040 050 104 151 166 151 144 145 144 040 142 171 040 070 054 040 164 157 040 164 150 145 040 156 145 141 162 145 163 164 040 151 156 164 145 147 145 162 054 040 141 156 144 040 164 150 145 156 040 160 154 165 163 040 146 157 165 162 051
```

Converting from **octal → ASCII**, yields:

```
How much did Maggie originally cost?
(Divided by 8, to the nearest integer, and then plus four)
```

This is the instruction to form the key.

---

## Determine the Key

Maggie Simpson prices canonically:

```
$847.63
```

Following instructions:

```
847.63 / 8 = 105.95
round -> 106
106 + 4 = 110
chr(110) = 'n'
```

Key is built with the formula:

```
key = key + key + chr(ord(key)-4)
```

So:

```
key = 'n' + 'n' + 'j'
key = "nnj"
```

---

## Decode Encoded Text

Encoded octal:

```
152 162 152 145 162 167 150 172 153 162 145 170 141 162
```

Octal → ASCII:

```
jrjerwhzkrexar
```

The cipher used is **Vigenere (alphabet based)**.

Formula:

```
plaintext = (cipher - key) mod 26
```

Decryption results:

```
wearenumberone
```

---

##Flags

```
wearenumberone
```

---

## Script Solver

```python
def octal_to_string(o):
    return ''.join(chr(int(x, 8)) for x in o.split())

def DecodeDat(key, text):
    res = ""
    for i in range(len(text)):
        c = ord(text[i]) - ord('a')
        k = ord(key[i % len(key)]) - ord('a')
        res += chr((c - k) % 26 + ord('a'))
    return res

encoded_octal = "152 162 152 145 162 167 150 172 153 162 145 170 141 162"

question_octal = (
"110 157 167 040 155 165 143 150 040 144 151 144 040 115 141 147 147 "
"151 145 040 157 162 151 147 151 156 141 154 154 171 040 143 157 163 "
"164 077 040 050 104 151 166 151 144 145 144 040 142 171 040 070 054 "
"040 164 157 040 164 150 145 040 156 145 141 162 145 163 164 040 151 "
"156 164 145 147 145 162 054 040 141 156 144 040 164 150 145 156 040 "
"160 154 165 163 040 146 157 165 162 051"
)

print(octal_to_string(question_octal))

price = 847.63
k = chr(round(price / 8) + 4)
key = k + k + chr(ord(k) - 4)

encoded = octal_to_string(encoded_octal)
print("Flag:", DecodeDat(key, encoded))
```
