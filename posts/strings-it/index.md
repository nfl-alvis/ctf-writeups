# PicoCTF 2019 – General Skills: Strings It




{{< admonition tldr "TL;DR" >}}

**picoCTF 2019 – General Skills: strings it** is a general skills challenge from **picoCTF**. Can you find the flag in the file without running it?

{{< /admonition >}}

## Description

Can you find the flag in the file without running it?

<details>
  <summary><h2>Hint</h2></summary>
  strings
</details>

---

## Objectives

The objective of this challenge is to extract the flag from a binary file **without executing it**, using static analysis techniques.

---

## Analysis

The provided file is identified as an ELF executable:

```bash
file strings
```

Output:

```
ELF 64-bit LSB pie executable, x86-64, dynamically linked, not stripped
```

Since running unknown binaries can be unsafe, the challenge explicitly requires analyzing it without executing.

A common technique in such cases is to inspect embedded human-readable strings inside the binary.

---

## Approach

We use the `strings` utility to extract printable sequences from the binary:

```bash
strings strings
```

Because the output is large, we filter it using `grep` to locate the flag pattern:

```bash
strings strings | grep -o "picoCTF{[^}]*}"
```

### Explanation

* `strings` extracts readable text from the binary
* `grep -o` ensures only the matching portion is printed
* `___FL0X___]*}` safely captures the flag format without overmatching


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/strings-it/  

