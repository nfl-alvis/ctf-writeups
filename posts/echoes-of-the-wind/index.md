# Echoes of the Wind



{{< admonition tldr "TL;DR" >}}

**Echoes of the Wind** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description
Given a ciphertext written using **Navajo Code** (World War II communications code).
Our task is to decode the message until we get the flag.

Flag format:

```
FGTE{WORD_WORD_WORD_WORD}
```

---

## Technical Analysis
Given ciphertext:

```
AH-NAH TSE-GAH THAN-ZIE
CHINDI A-CHIN YEH-HES GLOE-IH
KLESH JAD-HO-LONI WOL-LA-CHEE AH-NAH CLA-GI-AIH KLESH
DAH-NES-TSA WOL-LA-CHEE DZEH NASH-DOIE-TSO TLA-GIN
```

Navajo Code typically maps certain words to letters of the alphabet, numbers, or military words.
Therefore, the first step is to do decoding using the Navajo Code table.

---

## Completion

### 1. Decode Navajo Code
Ciphertext is decoded using online tools:

- https://www.dcode.fr/navajo-code

{{< image src="./images/solved.png" >}}

Decoding result:

```
E H T D N I W S K A E P S R A E L C
```

However, this plaintext still looks random and does not form sentences.

---

### 2. Pattern Analysis (Mirror / Reverse)
If these letters are combined, we get:

```
EHTDNIWSKAEPSRAELC
```

It turns out that the result is text that is **mirrored / reversed)** per word.
Since the flag format requires 4 words, arrange them in a form that makes sense, and the flag was successfully obtained.

---

## Flags

```
FGTE{REDACTED}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/echoes-of-the-wind/  

