# BruXOR



{{< admonition tldr "TL;DR" >}}

**BruXOR** is a crypto challenge from **CTFlearn**. ---

{{< /admonition >}}

---

## Description

There is a technique called brute force. 
Message: q{vpln'bH_varHuebcrqxetrHOXEj 
No keys! Just brute.. brute.. brute... :D

---

## Technical Analysis

Given an _encrypted string_ resulting in `XOR` with an unknown key, in the _challenge_ description it says `"Brute"` which means we have to bruteforce the key. 

---

## Decrypt process

Decrypting in [CyberChef](https://gchq.github.io/CyberChef) with Recipe `XOR Brute Force` .
In the bruteforce results, a valid plaintext was found at key = 17, with the following results:

{{< image src="./images/Decrypted.png" >}}

`Key = 17: ___FL0X___` ---

## Flags

```flag{y0u_Have_bruteforce_XOR}```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/bruxor/  

