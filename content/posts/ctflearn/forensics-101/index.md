---
title: "Forensics 101"
date: 2026-02-22
categories:
  - Forensics
tags:
  - Forensics
  - CTFlearn
collections:
  - CTFlearn
---



{{< admonition tldr "TL;DR" >}}

**Forensics 101** is a forensics challenge from **CTFlearn**. Think the flag is somewhere in there. Would you help me find it? [Chall.jpg](https://mega.nz/#!OHohCbTa!wbg60PARf4u6E6juuvK9-aDRe_bgEL937VO01EImM7c)

{{< /admonition >}}

## Description

Think the flag is somewhere in there. Would you help me find it?
[Chall.jpg](https://mega.nz/#!OHohCbTa!wbg60PARf4u6E6juuvK9-aDRe_bgEL937VO01EImM7c)

---

## Technical Analysis

We are provided with the image file `Chall.jpg` , although in image format, this file may contain **hidden ASCII** strings that can be extracted.

{{< image src="./images/Chall.jpg" >}}

To carry out initial analysis, use the `strings` command piped with `grep` to search for the word flag.

```bash
strings Chall.jpg | grep flag
```

Output:

```
flag{wow!_data_is_cool}
```

From the results it can be concluded that the flag is stored in plaintext in the image file.

---

##Flags

```flag{wow!_data_is_cool}```
