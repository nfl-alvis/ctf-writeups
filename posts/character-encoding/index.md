# Character Encoding



{{< admonition tldr "TL;DR" >}}

**Character Encoding** is a crypto challenge from **CTFlearn**. In the computing industry, standards are established to facilitate information interchange among American coders. Unfortunately, I've made communication a little bit more difficult. Can you figure this one out? 41 42 43 54 46 7B 34 35 43 31 31 5F 31 35 5F 55 35 33 46 55 4C 7D

{{< /admonition >}}

## Description

In the computing industry, standards are established to facilitate information interchange among American coders. Unfortunately, I've made communication a little bit more difficult. Can you figure this one out? 41 42 43 54 46 7B 34 35 43 31 31 5F 31 35 5F 55 35 33 46 55 4C 7D

---

## Analyze

Given a series of values in _**hexadecimal**_ format:

```
41 42 43 54 46 7B 34 35 43 31 31 5F 31 35 5F 55 35 33 46 55 4C 7D
```

These values resemble an **ASCII** representation that has been converted to _*hexadecimal*_ form.

---

## Decode

To decode this data, use the `xxd` command with the `-r -p` option which functions to convert hexadecimal input into **ASCII** character representation.

```bash
echo '41 42 43 54 46 7B 34 35 43 31 31 5F 31 35 5F 55 35 33 46 55 4C 7D' | xxd -r -p
```

Output:

```
ABCTF{45C11_15_U53FUL}
```

---

## Flags

```ABCTF{45C11_15_U53FUL}```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/character-encoding/  

