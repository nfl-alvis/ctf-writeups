---
title: "picoCTF 2026 – General Skills: Bytemancy"
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
**picoCTF 2026 – General Skills: Bytemancy** is a misc challenge from **picoCTF**. This challenge presents a simple Python program that validates user input and reveals the flag only if the input matches a specific byte sequence. The goal is to analyze the provided source code and determine the correct input required to satisfy the condition.
{{< /admonition >}}

## Overview

This challenge presents a simple Python program that validates user input and reveals the flag only if the input matches a specific byte sequence. The goal is to analyze the provided source code and determine the correct input required to satisfy the condition.

The service can be accessed remotely using `nc` (netcat).

---

## Provided Source Code

The challenge includes the following Python code:

```python
while(True):
  try:
    print('⊹──────[ BYTEMANCY-0 ]──────⊹')
    print("☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐")
    print()
    print('Send me ASCII DECIMAL 101, 101, 101, side-by-side, no space.')
    print()
    print("☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐☉⟊☽☈⟁⧋⟡☍⟐")
    print('⊹─────────────⟡─────────────⊹')
    user_input = input('==> ')
    if user_input == "\x65\x65\x65":
      print(open("./flag.txt", "r").read())
      break
    else:
      print("That wasn't it. I got: " + str(user_input))
      print()
      print()
      print()
  except Exception as e:
    print(e)
    break
```

---

## Understanding the Logic

The program repeatedly asks the user for input and checks whether the input matches a specific byte sequence:

```python
if user_input == "\x65\x65\x65":
```

The sequence `\x65` represents a hexadecimal byte value.

---

## Converting the Byte Values

The challenge prompt states:

```
Send me ASCII DECIMAL 101, 101, 101, side-by-side, no space.
```

ASCII decimal value **101** corresponds to the character **`e`**.

Conversion:

| Decimal | Hex  | Character |
| ------- | ---- | --------- |
| 101     | 0x65 | e         |

Therefore:

```
\x65\x65\x65
```

is equivalent to:

```
eee
```

---

## Connecting to the Service

The remote service can be accessed using netcat:

```bash
nc candy-mountain.picoctf.net 61501
```

---

## Sending the Correct Input

When prompted, send:

```
eee
```

Example interaction:

```
⊹──────[ BYTEMANCY-0 ]──────⊹

Send me ASCII DECIMAL 101, 101, 101, side-by-side, no space.

==> eee
```

---

## Retrieving the Flag

After sending the correct input, the program prints the contents of `flag.txt`.

Output:

```
picoCTF{pr1n74813_ch4r5_62360bfd}
```

---

## Final Flag

```
picoCTF{pr1n74813_ch4r5_62360bfd}
```

---

## Key Takeaways

* ASCII characters can be represented in decimal, hexadecimal, or byte escape sequences.
* The escape sequence `\x65` corresponds to the ASCII character `e`.
* Understanding how programs compare input values is essential when solving CTF challenges involving encoding or byte manipulation.
* Reading and analyzing source code often reveals the exact input required to trigger hidden functionality.
