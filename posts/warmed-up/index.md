# PicoCTF 2019 – General Skills: Warmed Up


{{< admonition tldr "TL;DR" >}}
**picoCTF 2019 – General Skills: Warmed Up** is a general skills challenge from **picoCTF**. What is `0x3D` (base 16) in decimal (base 10)?
{{< /admonition >}}

## Description

What is `0x3D` (base 16) in decimal (base 10)?

<details>
  <summary><h2>Hint</h2></summary>
  Submit your answer in our flag format. For example, if your answer was '22', you would submit 'picoCTF{22}' as the flag.
</details>

---

## Objective

The objective of this challenge is to convert a hexadecimal (base 16) number into its decimal (base 10) equivalent.

---

## Analysis

The given value is:

```
0x3D
```

In hexadecimal:

* The digit `3` is in the 16¹ place
* The digit `D` represents 13 in decimal and is in the 16⁰ place

---

## Calculation

We compute the value as follows:

* 3 × 16 = 48
* D (13) × 1 = 13

Total:

```
48 + 13 
```

---

## Alternative Approach

You can also calculate this using the `bc` command-line tool:

```bash
echo "ibase=16; 3D" | bc
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/warmed-up/  

