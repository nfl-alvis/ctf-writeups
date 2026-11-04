# Vigenere Cipher



{{< admonition tldr "TL;DR" >}}

**Vigenere Cipher** is a crypto challenge from **CTFlearn**. The vignette cipher is a method of encrypting alphabetic text by using a series of interwoven Caesar ciphers based on the letters of a keyword. I'm not sure what this means, but it was left lying around: blorpy

{{< /admonition >}}

## Description

The vignette cipher is a method of encrypting alphabetic text by using a series of interwoven Caesar ciphers based on the letters of a keyword.
I'm not sure what this means, but it was left lying around: blorpy

gwox{RgqssihYspOntqpxs}

---

## Technical Analysis

The ciphertext provided indicates the use of the **Vigenère cipher**, with the _keyword_ provided, namely `blorpy` .
Assuming encryption is carried out in a standard alphabet (A–Z), the decryption process can be carried out using the Vigenère decryption method.

---

## Decryption Process

_Decrypt_ in [CyberChef](https://gchq.github.io/CyberChef/), get results:

{{< image src="./images/Decrypted.png" >}}

---

## Flags

```flag{CiphersAreAwesome}```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/vigenere-cipher/  

