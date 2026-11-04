---
title: "QR Vault"
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

**QR Vault** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Solution Steps

### 1. Scan the QR Code from the Image
The first step is to read the contents of the QR Code using `zbarimg` .

```bash
zbarimg chall.png
```
The output indicates that the QR Code was successfully read and produced a long string:

```
QR-Code:UEsDBAoAAAAAANwRO1uub+7rIgAAACIAAAAIABwAZmxhZy50eHRVVAkAA6BI12igSNdodXgLAAEE
6AMAAAToAwAARkdURXtaaXBCYXNlNjRfUVJfQ2hhbGxlbmdlXzIwMjV9ClBLAQIeAwoAAAAAANwR
O1uub+7rIgAAACIAAAAIABgAAAAAAAEAAAD/gQAAAABmbGFnLnR4dFVUBQADoEjXaHV4CwABBOgD
AAAE6AMAAFBLBQYAAAAAAQABAE4AAABkAAAAAAA=
```

---

### 2. Decode Base64 String to Output File

The scanned string looks like Base64. The data is then decoded and saved to a file called `output` .

```bash
echo "UEsDBAoAAAAAANwRO1uub+7rIgAAACIAAAAIABwAZmxhZy50eHRVVAkAA6BI12igSNdodXgLAAEE
6AMAAAToAwAARkdURXtaaXBCYXNlNjRfUVJfQ2hhbGxlbmdlXzIwMjV9ClBLAQIeAwoAAAAAANwR
O1uub+7rIgAAACIAAAAIABgAAAAAAAEAAAD/gQAAAABmbGFnLnR4dFVUBQADoEjXaHV4CwABBOgD
AAAE6AMAAFBLBQYAAAAAAQABAE4AAABkAAAAAAA=" | base64 -d > output
```

---

### 3. Identify the Decoded File Format

To find out the actual format of the `output` file, check it using `file` .

```bash
file output
```

The result:

```
output: Zip archive data, made by v3.0 UNIX, extract using at least v1.0, last modified Sep 27 2025 02:14:56, uncompressed size 34, method=store
```

This proves that the Base64 data is a **ZIP archive** wrapped in a QR Code.

---

### 4. Extract ZIP Archive

Since the file is a ZIP, the next step is to extract it using `unzip` .

```bash
unzip output
```

Output:

```
Archive:  output
 extracting: flag.txt
```

---

### 5. Reading Flags

After extracting, you will get the `flag.txt` file. The contents of the file are read using `cat` .

```bash
cat flag.txt
```

Results:

```
FGTE{Redacted}
```

---

##Flags

```
FGTE{Redacted}
```
