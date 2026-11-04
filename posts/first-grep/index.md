# PicoCTF 2019 – General Skills: First Grep




{{< admonition tldr "TL;DR" >}}

**picoCTF 2019 – General Skills: First Grep** is a general skills challenge from **picoCTF**. Can you find the flag in the file? This would be really boring to look through manually, something tells me there is a better way. The flag is in this file.

{{< /admonition >}}

## Description

Can you find the flag in the file? This would be really boring to look through manually, something tells me there is a better way.
The flag is in this file.

<details>
  <summary><h2>Hint</h2></summary>
  grep tutorial
</details>

---

## Objectives

The goal of this challenge is to efficiently locate a hidden flag inside a large file without manually inspecting its contents.

---

## Analysis

The provided file contains a large amount of seemingly random and unreadable data. Viewing it directly using:

```bash
cat file
```

produces a long stream of characters, making manual inspection practical.

This suggests that the file may contain embedded readable strings among binary or obfuscated data.

---

## Approach

To extract readable content from the file, the `strings` utility can be used. This tool scans binary data and outputs sequences of printable characters.

```bash
strings file
```

However, since the output is still quite large, we refine the process by filtering only the lines that match the expected flag format using `grep` .

The flag format for picoCTF is typically:

```
picoCTF{...}
```

Thus, we use the following command:

```bash
strings file | grep -o "picoCTF{[^}]*}"
```

### Explanation

* `strings file` extracts readable strings from the file.
* `grep -o` ensures that only the matching portion is displayed.
* `___FL0X___]*}` is a regular expression that:

  * Matches the prefix `picoCTF{` * Captures any characters except `}` until the closing brace is found


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/first-grep/  

