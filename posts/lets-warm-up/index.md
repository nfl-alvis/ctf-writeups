# PicoCTF 2019 – General Skills: Lets Warm Up


{{< admonition tldr "TL;DR" >}}
**picoCTF 2019 – General Skills: Lets Warm Up** is a general skills challenge from **picoCTF**. If I told you a word started with `0x70` in hexadecimal, what would it start with in ASCII?
{{< /admonition >}}

## Description

If I told you a word started with `0x70` in hexadecimal, what would it start with in ASCII?

<details>
  <summary><h2>Hint</h2></summary>
  Submit your answer in our flag format. For example, if your answer was 'hello', you would submit 'picoCTF{hello}' as the flag.
</details>

---

## Objective

The objective of this challenge is to convert a hexadecimal value into its corresponding ASCII character.

---

## Analysis

The given value is:

```bash
0x70
```

This represents a hexadecimal number that corresponds to an ASCII character.

In ASCII encoding:

* Each hexadecimal value maps to a specific character
* `0x70` corresponds to the decimal value `112`

Looking up ASCII value `112` results in the character:

```bash
p
```

---

## Approach

We can verify this using the `xxd` command:

```bash
echo "70" | xxd -r -p
```

Output:

```bash
p
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/lets-warm-up/  

