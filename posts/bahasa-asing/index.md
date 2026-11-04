# Bahasa Asing?



{{< admonition tldr "TL;DR" >}}

**Foreign Language?** is a crypto challenge from **FGTE**. A scientist finds a box containing bread and a piece of paper written in what appears to be a foreign language. Underneath there is a note:

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Challenge Description

A scientist finds a box containing bread and a piece of paper written in what appears to be a foreign language. Underneath there is a note:

> **“Find the base.”**

### Hints

1. *"Are you sure it's really a foreign language? Try to pay attention to the small details."*
2. *"Have you ever seen an encoding that utilizes the entire Unicode range to store data? It's similar to the one that produces those big Chinese characters..."*

Given the following string:

```
=︪ڔౢ︪ɫ൸ծౢݚ卑ɬE൸୞︦神ѡݚ酤
```

---

## Step 1 — Initial Observations

At first glance the string looks like a mixture of characters:

* Unicode symbols
* Characters similar to Chinese characters
* Latin letters
* Other non-ASCII characters

From the second hint, the encoding mentioned is:

> “utilizes the entire Unicode range”

This is a strong pointer towards **Base65536**, which is an encoding scheme that uses Unicode characters (generally CJK characters) to represent binary data.

---

## Step 2 — Identify Base65536

Referring to the instructions *"find the base"*, it is assumed that the string is the result of number-based encoding (base encoding).

After performing research, it was found that the character pattern used was consistent with **Base65536**.

However, when directly decoding Base65536, no valid flags were obtained. The output is still strange and unreadable characters.

This indicates that Base65536 is not the first layer.

---

## Step 3 — Additional Layer Analysis

Another important clue is found in the narrative:

> “box containing bread”

The word *bread* can be associated with the word **“rot”** , leading to the possible use of the **ROT cipher**.

In the context of Unicode, there are variants of ROT that work on a wide character range, namely:

* **ROT8000**

The ROT8000 is known to produce large CJK/Unicode characters, as per the hint:

> “those big Chinese characters…”

---

## Step 4 — Decode the ROT8000 First

The correct steps are:

1. **Decode ROT8000**
2. Then **Decode Base65536**

After performing ROT8000 first, the output becomes a valid Base65536 representation.

```
籆穔艻衉穔繒襟腕衉荁癟繓籎襟蝅穐癃聈荁ᕽ
```

Then Base65536 decoding is carried out.

{{< image src="./images/solved.png" >}}

The flag is obtained after both layers have been successfully decoded in the correct order

---

## Flags

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/bahasa-asing/  

