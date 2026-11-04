---
title: "Extensions"
date: 2026-02-22
categories:
  - Misc
tags:
  - Misc
  - FGTE
collections:
  - FGTE
---



{{< admonition tldr "TL;DR" >}}

**Extensions** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description
Challenge provides a ZIP file containing several files with **intentionally obscured extensions**.  
The objective of the challenge is to identify the original format of each file, correct the extension, and then retrieve the flag pieces hidden within.

---

## Solution Steps

### 1. Extract the ZIP file
First, extract the given archive:

```bash
unzip extension.zip
```
Extraction results:

* `file1.csv` * `file2.go` * `file3.sh` ---

### 2. Identify the file format with `file` Since the extension doesn't look right, check the file signature:

```bash
file file1.csv file2.go file3.sh
```

From the output you can see:

* `file1.csv` is actually **PNG**
* `file2.go` is actually **PDF**
* `file3.sh` is actually a **GIF**

---

### 3. Take the flag chunk from the first file (PNG)

Since the first file is a PNG, the flag chunk can be searched using `zsteg` :

```bash
zsteg file1.csv
```

The output indicates plaintext on the bit-plane:

{{< image src="./images/flag1.png" >}}

---

### 4. Take the flag fragment from the second file (PDF)

The second file is detected as a PDF, so simply change its name and open it:

```bash
mv file2.go file2.pdf
xdg-open file2.pdf
```

Inside the PDF there is a barcode that holds the second flag fragment:

{{< image src="./images/flag2.png" >}}

---

### 5. Take the flag fragment from the third file (GIF)

The third file is detected as a GIF, so rename it and open it:

```bash
cp file3.sh recovered3.gif
xdg-open recovered3.gif
```

At the top left of the GIF there is a final flag piece:

{{< image src="./images/flag3.png" >}}

---

### 6. Combine all the pieces

Once the three parts are obtained, combine them into one complete flag string with standard format.

---

##Flags

```
FGTE{REDACTED}
```
