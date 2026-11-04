# PicoCTF 2019 – Forensics: Extensions




{{< admonition tldr "TL;DR" >}}

**picoCTF 2019 – Forensics: extensions** is a forensics challenge from **picoCTF**. This is a really weird text file. Can you find the flag? Get the flag from [TXT](https://challenge-files.picoctf.net/c_fickle_tempest/31fe772e6a4c71e867af0b2a93818e06d8f8ebf8af2a9615495d00356ff576da/flag.txt).

{{< /admonition >}}

## Description

This is a really weird text file. Can you find the flag?
Get the flag from [TXT](https://challenge-files.picoctf.net/c_fickle_tempest/31fe772e6a4c71e867af0b2a93818e06d8f8ebf8af2a9615495d00356ff576da/flag.txt).

<details>
  <summary><h2>Hint1</summary>
  How do operating systems know what kind of file it is? (It's not just the ending!)
</details>

<details>
  <summary><h2>Hint1</summary>
  Make sure to submit the flag as picoCTF{XXXXX}
</details>

---

## Objectives

The objective of this challenge is to identify the real file type and extract the hidden flag.

---

## Analysis

Although the file is named `flag.txt` , the hint suggests that the file extension may be misleading.

We verify the actual file type using the `file` command:

```bash
file flag.txt
```

Output:

```bash
flag.txt: PNG image data, 1697 x 608, 8-bit/color RGB, non-interlaced
```

This indicates that the file is actually a **PNG image**, not a text file.

---

## Approach

### 1. Rename the File

Since the file is identified as a PNG image, we change its extension:

```bash
cp flag.txt flag.png
```

---

### 2. Extract the Content

The image contains text, so we use OCR to extract it:

```bash
tesseract flag.png output
```

---

### 3. Retrieve the Results

```bash
cat output.txt
```

From this output, we obtain the flag.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/extensions/  

