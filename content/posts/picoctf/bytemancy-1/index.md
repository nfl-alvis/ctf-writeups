---
title: "picoCTF 2026 – Bytemancy 1"
date: 2026-03-10
categories:
  - Misc
tags:
  - Misc
  - picoCTF
collections:
  - picoCTF
---


{{< admonition tldr "TL;DR" >}}

**picoCTF 2026 – Bytemancy 1** is a misc challenge from **picoCTF**. The challenge provides a Python script and a remote service. We are asked to send **ASCII decimal 101** repeated **1751 times**, side-by-side without spaces.

{{< /admonition >}}

## Description

The challenge provides a Python script and a remote service.
We are asked to send **ASCII decimal 101** repeated **1751 times**, side-by-side without spaces.

Services:

```
nc foggy-cliff.picoctf.net 54825
```

Source files:

```
app.py
```

---

## Source Code Analysis

```python
while(True):
  try:
    print('Send me ASCII DECIMAL 101 1751 times, side-by-side, no space.')
    user_input = input('==> ')
    if user_input == "\x65"*1751:
      print(open("./flag.txt", "r").read())
      break
```

Key parts:

```python
if user_input == "\x65"*1751:
```

Important observation:

* `\x65` is **hexadecimal ASCII for character `e` **
* ASCII decimal **101 = character `e` **
* The program checks if the input equals:

```
"e" repeated 1751 times
```

So the correct input is:

```
eeeeeeeeeeee... (1751 times)
```

---

## Exploitation

We can generate the required string easily using Python:

```bash
python3 -c 'print("e"*1751)'
```

Then pipe it to the remote service:

```bash
python3 -c 'print("e"*1751)' | nc foggy-cliff.picoctf.net 54825
```

---

## Solve Script `solve.sh`

```bash
#!/bin/bash

HOST=$1
PORT=$2

python3 -c 'print("e"*1751)' | nc $HOST $PORT
```

Run:

```bash
chmod +x solve.sh
./solve.sh foggy-cliff.picoctf.net 54825
```

---

## Flags

```
picoCTF{h0w_m4ny_e's???_62edb104}
```

---

## Key Takeaways

* ASCII **decimal 101** corresponds to the character ** `e` **.
* `\x65` is the hexadecimal representation of ASCII `e` .
* The challenge simply required generating a string with **1751 repetitions of `e` **.

This is a beginner challenge demonstrating **ASCII encoding and simple scripting for automation**.
