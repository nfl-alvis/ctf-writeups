---
title: "television"
date: 2026-02-22
categories:
  - Misc
tags:
  - Misc
  - ctf.hackme.quest
collections:
  - ctf.hackme.quest
---



{{< admonition tldr "TL;DR" >}}

**television** is a misc challenge from **ctf.hackme.quest**. Looks like my television was broken

{{< /admonition >}}

## Challenge Description

Looks like my television was broken

---

## Technical Analysis

We are provided with an image file named `television.bmp` .

{{< image src="./images/television.bmp" >}}

The initial step taken is to extract strings that may be stored in the file using `strings` , then filter the results with `grep` to look for the keyword `FLAG` .

```bash
strings television.bmp | grep FLAG
```

From the command, the following flag was discovered:

```FLAG{PuRe_R@ND0M_DaTa_Fr0M/D3V/UR@ND0M}```

---

##Flags

```FLAG{PuRe_R@ND0M_DaTa_Fr0M/D3V/UR@ND0M}```
