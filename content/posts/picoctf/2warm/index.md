---
title: "picoCTF 2019 – General Skills: 2warm"
date: 2026-03-23
categories:
  - General Skills
tags:
  - General Skills
  - picoCTF
collections:
  - picoCTF
---

{{< admonition tldr "TL;DR" >}}
**picoCTF 2019 – General Skills: 2warm** is a general skills challenge from **picoCTF**. Can you convert the number **42 (base 10)** to **binary (base 2)**?
{{< /admonition >}}

## Description

Can you convert the number **42 (base 10)** to **binary (base 2)**?

<details>
  <summary><h2>Hint</summary>
  Submit your answer in our competition's flag format. For example, if your answer was '11111', you would submit 'picoCTF{11111}' as the flag.
</details>

---

## Analysis

This challenge tests a fundamental concept in computer science: **number base conversion**, specifically converting a decimal number (base 10) into its binary representation (base 2).

The given value is:

```
42 (base 10)
```

The task is to determine its equivalent in binary form.

---

## Solution

### Method 1: Using `bc`

The `bc` (Basic Calculator) utility in Linux can perform base conversions.

Command:

```bash
echo "obase=2; 42" | bc
```

Output:

```
101010
```

---

### Method 2: Using Python

An alternative approach is to use Python’s built-in conversion function:

```bash
python3 -c "print(bin(42)[2:])"
```

Output:

```
101010
```

---

### Method 3: Manual Conversion

The conversion can also be performed manually by repeatedly dividing the number by 2 and recording the remainders:

| Division | Quotient | Remainder |
| -------- | -------- | --------- |
| 42 ÷ 2   | 21       | 0         |
| 21 ÷ 2   | 10       | 1         |
| 10 ÷ 2   | 5        | 0         |
| 5 ÷ 2    | 2        | 1         |
| 2 ÷ 2    | 1        | 0         |
| 1 ÷ 2    | 0        | 1         |

Reading the remainders from bottom to top yields:

```
101010
```

---

## Flag

```
picoCTF{101010}
```
