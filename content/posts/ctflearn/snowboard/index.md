---
title: "Snowboard"
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

**Snowboard** is a forensics challenge from **CTFlearn**. ---

{{< /admonition >}}

---

## Description

Find the flag in the jpeg file. Good Luck!  
[Snowboard.jpg](https://ctflearn.com/challenge/download/934)

---

## Technical Analysis

Given an image `Snowboard.jpg` .

{{< image src="./images/Snowboard.jpg" >}}

The initial step taken is to search for _plaintext_ that may be hidden in the file using the `strings` utility.

```bash
strings Snowboard.jpg | grep CTF
```

Output:

```
CTFlearn{CTFIsEasy!!!}
```

The result looks like _flag_, but after I input the flag it turns out it's wrong and it's just **decoy _flag_**.

---

## Hexadecimal Technical Analysis

Next, a hexadecimal inspection was performed on the file to look for suspicious data patterns, and a string was found that resembled **Base64**.

```bash
xxd Snowboard.jpg | head |  awk '{print $NF}'
```

Output:

```
......JFIF.....,
.,......CTFlearn
{CTFIsEasy!!!}..
...Q1RGbGVhcm57U
2tpQmFuZmZ9Cg==.
..#.Exif..II*...
............n...
........t.......
................
....(...........
```

You can see the string:

```
Q1RGbGVhcm57U2tpQmFuZmZ9Cg==
```

The pattern is very typical as _**Base64** encoding_.

---

## Decode Base64

The Base64 string is then decoded using the following command:

```bash
echo "Q1RGbGVhcm57U2tpQmFuZmZ9Cg==" | base64 -d
```

Decoding result:

```
CTFlearn{SkiBanff}
```

---

##Flags

```CTFlearn{SkiBanff}```
