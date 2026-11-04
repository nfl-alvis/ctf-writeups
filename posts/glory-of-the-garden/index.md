# PicoCTF 2019 – Forensics: Glory of the Garden




{{< admonition tldr "TL;DR" >}}

**picoCTF 2019 – Forensics: Glory of the Garden** is a forensics challenge from **picoCTF**. This file contains more than it seems. Get the flag from [garden.jpg](https://challenge-files.picoctf.net/c_fickle_tempest/39ad2588c3c0db341eff579d7cf894efc34a3b8174368eee2ea0e5ea06516238/garden.jpg).

{{< /admonition >}}

## Description

This file contains more than it seems.
Get the flag from [garden.jpg](https://challenge-files.picoctf.net/c_fickle_tempest/39ad2588c3c0db341eff579d7cf894efc34a3b8174368eee2ea0e5ea06516238/garden.jpg).

<details>
  <summary><h2>Hint</h2></summary>
  What is a hex editor?
</details>

---

## Objectives

The objective of this challenge is to extract hidden information from an image file using forensic analysis techniques.

---

## Analysis

The challenge suggests that the image contains hidden data. In many CTF forensics challenges, files (especially images) may include:

* Embedded strings
* Hidden metadata
* Appended data after the file structure

Instead of visually inspecting the image, we perform **static analysis** using tools like `strings` .

---

## Approach

We extract readable strings from the image and filter for the flag format:

```bash
strings garden.jpg | grep -o "picoCTF{.*}"
```

### Explanation

* `strings` extracts printable characters from the file
* `grep -o` prints only the matching portion
* `___FL0X___` searches for the flag pattern


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/glory-of-the-garden/  

