# Modern Gaius Julius Caesar



{{< admonition tldr "TL;DR" >}}

**Modern Gaius Julius Caesar** is a crypto challenge from **CTFlearn**. ---

{{< /admonition >}}

---

##Challenge

One of the easiest and earliest known ciphers but with a XXI century twist! Nobody uses Alphabet nowadays right? Why should you when you have your keyboard?  
  
BUH'tdy,|Bim5y~Bdt76yQ

---

## Technical Analysis

Given an encrypted string, judging from the description of the problem, I assume the encrypted result is from the keyboard shift cipher

---

## Decrypt

To decrypt the ciphertext, use the Keyboard Shift Cipher Decoder tool in [dcode.fr](https://www.dcode.fr/keyboard-shift-cipher), resulting in:

{{< image src="./images/decrypted.png" >}}

By trying several shifting directions, plaintext was successfully obtained.

---

## Flags

```CTFlearn{Cyb3r_Cae54r}```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/modern-gaius-julius-caesar/  

