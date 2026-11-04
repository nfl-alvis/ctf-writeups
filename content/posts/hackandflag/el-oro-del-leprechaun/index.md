---
title: "1. Bruteforce Zip Pass"
date: 2026-05-03
categories:
  - Crypto
tags:
  - Crypto
  - HackAndFlag
collections:
  - HackAndFlag
---

{{< admonition tldr "TL;DR" >}}
**1. Bruteforce Zip Pass** is a crypto challenge from **HackAndFlag**. ```john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt```
{{< /admonition >}}

```john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt```

Result :

```
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
trasgo           (flag.zip/flag.txt)
1g 0:00:00:01 DONE (2026-05-03 22:37) 0.7874g/s 2438Kp/s 2438Kc/s 2438KC/s trina1981..trapp15
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

# 2. Unzip

```unzip flag.zip
Archive:  flag.zip
replace clue.txt? [y]es, [n]o, [A]ll, [N]one, [r]ename: y
  inflating: clue.txt
[flag.zip] flag.txt password:
  inflating: flag.txt
```

read flag

```cat flag.txt
mc51{n0000000000000_m1_020000000000000}%
```
