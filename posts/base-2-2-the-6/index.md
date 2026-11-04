# Base 2 2 the 6



{{< admonition tldr "TL;DR" >}}

**Base 2 2 the 6** is a crypto challenge from **CTFlearn**. There are so many different ways of encoding and decoding information nowadays... One of them will work! Q1RGe0ZsYWdneVdhZ2d5UmFnZ3l9

{{< /admonition >}}

## Description

There are so many different ways of encoding and decoding information nowadays... One of them will work! Q1RGe0ZsYWdneVdhZ2d5UmFnZ3l9

---

## Technical Analysis

Given a _string_

```
Q1RGe0ZsYWdneVdhZ2d5UmFnZ3l9
```

Based on the string structure consisting of alphanumeric characters ( `A–Z` , `a–z` , `0–9` ) without non-standard characters, it can be assumed that the data is encoded with **Base64 encoding**.

---

## Decode Process

The decoding process is carried out using the command `base64` with the `-d` option, namely to decode.

```bash
echo 'Q1RGe0ZsYWdneVdhZ2d5UmFnZ3l9' | base64 -d
```

Output:

```
CTF{FlaggyWaggyRaggy}
```

---

## Flags

```CTF{FlaggyWaggyRaggy}```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/base-2-2-the-6/  

