# ZigZag



{{< admonition tldr "TL;DR" >}}

**ZigZag** is a crypto challenge from **FGTE**. Challenge provides a ciphertext:

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Challenge Description

Challenge provides a ciphertext:

```
FZAMBRYGEIZGIPEUTIKTGSLTC
```

The question title is **ZigZag**, and the flag format is requested:

```
FGTE{WORD_WORD_WORD_WORD}
```

---

## Solution Steps

### 1. Identify Ciphers

From the title of the **ZigZag** challenge, the most commonly used method is:

- **Rail Fence Cipher** (Zig-Zag Cipher)

Rail Fence works by writing plaintext in a zig-zag manner onto several lines (rails), then reading each line to form ciphertext.

---

### 2. Rail Fence Decryption

Ciphertext is decrypted using the Rail Fence tool in:

- https://www.dcode.fr/rail-fence-cipher

After trying several rail counts, a valid plaintext was found:

{{< image src="./images/solved.png" >}}

---

### 3. Customize the Flag Format

The plaintext already contains the appropriate words, it just needs to be arranged into the `___FL0X___` flag format with the `_` separator.

---

## Flags

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/zigzag/  

