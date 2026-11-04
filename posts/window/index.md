# Window



{{< admonition tldr "TL;DR" >}}

**window** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

Given an image containing a black/white square pattern that looks like a *cipher grid*.

{{< image src="./images/window.png" >}}

Challenge gives a hint:

- 🪟 (window)
- **frideric cipher**
- Flag format: `___FL0X___` The target is to decode the contents of the message from the grid to get the flag.

---

## Solution Steps

### 1. Identify the Cipher Type

Based on the **Friderici cipher** hint, the image matches the shape of the *Friderici cipher grid*

---

### 3. Decoding Results

Reading the decode results, we get the output, which is a flag that is still invalid.  
Takes the sentence after the occurrence of the string `FGTE` .  
It can be seen that:

- `FGTE` appears clearly
- many letters `X` appear as separators between words

---

### 4. Normalize Output to Flag Format

In the Friderici cipher, the character `X` is commonly used as a separator/filler.
So the string is normalized by the rule:

- `X` → `_` - organize into `___FL1X___` format 

The final result becomes:

```
FGTE{REDACTED}
```

---

## Flags

```
FGTE{REDACTED}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/window/  

