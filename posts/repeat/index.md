# Repeat



{{< admonition tldr "TL;DR" >}}

**repeat** is a crypto challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

The challenge provides a long ciphertext, with a hint:

- `13 -> 64` The question description also explains that the message is hidden in **repeated encoding layers**, so that after one layer is revealed, there is still another layer.

From this hint it can be concluded that each layer uses a combination of:

- **ROT13**
- **Base64**

which must be repeated continuously until the final plaintext appears.

---

## Solution Steps

### 1. Identify Transformation Patterns

The ciphertext provided is still in the form of Base64 characters ( `A–Z a–z 0–9 + / =` ), but the final result cannot be read directly.

Hint `13 -> 64` leads to process sequence:

1. ROT13 ( `13` )
2. Base64 decode ( `64` )

Because the layers are repeated, this pipeline must be repeated several times until the result is no longer in Base64 form.

---

### 2. Decrypt Using CyberChef (Recursive)

The easiest way to do repeated decoding is to use **CyberChef**, because it provides a *Jump* feature to repeat the recipe.

Recipe used:

1. `Label` (as jump target)
2. `ROT13` 3. `From Base64` 4. `Jump` (return to label to repeat)

By configuration:

- ROT13: `Amount = 13` - From Base64: default alphabet, non-alphabet chars removed
- Jump: `Maximum jumps` is increased so that the process can repeat several times

---

### 3. Execute Decode Repeatedly

After the recipe is run, the output changes layer by layer until it finally produces a string that matches the flag format.

{{< image src="./images/solved.png" >}}

---

## Flags

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/repeat/  

