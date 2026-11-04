# Mirror & Shift



{{< admonition tldr "TL;DR" >}}

**Mirror & Shift** is a crypto challenge from **FGTE**. Challenge provides a ciphertext:

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Challenge Description

Challenge provides a ciphertext:

```
HGTI{IZK0J1ZG_TV4ZUH0VAMU1_KVOXT0}
```

Hint says the message is hidden in **two letter transformation layers**.  
The target is to find flags with the format `___FL0X___` .

---

## Solution Steps

### 1. Identify the Cipher Type

The first step is to try to identify the cipher used.

Ciphertext is entered into the **Cipher Identifier** feature in `dcode.fr` .  
From the result identification, the cipher with the highest probability is:

- **Affine Cipher**

---

### 2. Decrypt Using Affine Cipher (Bruteforce)

After selecting the **Affine Cipher** menu in dcode, the ciphertext is entered and then decrypted using the following features:

- **Decrypt (Bruteforce)**

From the bruteforce results, a combination of parameters is found that produces valid plaintext:

- `A = 25` - `B = 12`

{{< image src="./images/solved.png" >}}

---

## Flags

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/mirror-shift/  

