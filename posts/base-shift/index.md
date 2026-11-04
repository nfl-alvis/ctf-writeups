# Base & Shift



{{< admonition tldr "TL;DR" >}}

**Base & Shift** is a crypto challenge from **FGTE**. The challenge provides a Base64 ciphertext:

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Challenge Description

The challenge provides a Base64 ciphertext:

```
U1RHUntPbmZyNjRfTmFxX0ViZzEzfQ==
```
Based on the description, the message is hidden in **two layers of transformation**:

1. Plaintext results in-**ROT13**
2. The ROT13 output is then **Base64**

The target is to reverse this process to obtain a flag with the format `___FL0X___` .

---

## Solution Steps

### 1. Decode Base64

Because the last layer is Base64, The first step is to decode Base64:

```bash
echo "U1RHUntPbmZyNjRfTmFxX0ViZzEzfQ==" | base64 -d
```
The output obtained is still not in the form of a final flag (because it is still affected by ROT13).

---

### 2. Decode ROT13

Next, the results from Base64 need to be ROT13 to restore the original plaintext.

On Linux, ROT13 can be done using `tr` :

```bash
echo "U1RHUntPbmZyNjRfTmFxX0ViZzEzfQ==" | base64 -d | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

---

## Flags

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/base-shift/  

