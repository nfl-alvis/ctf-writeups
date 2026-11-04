---
title: "Unzip Ini!!"
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

**Unzip This!!** is a misc challenge from **FGTE**. Our job is to repair the ZIP file, extract its contents, then get the flag.

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Challenge Description
We are provided with a ZIP file named `zxcasdqwe.zip` .
But when extracted using `unzip` , the file always fails because the ZIP structure is damaged.

Our job is to repair the ZIP file, extract its contents, then get the flag.

---

## Technical Analysis
During extraction:

```bash
unzip zxcasdqwe.zip
```
error appears:

```
bad zipfile offset (local header sig):  0
```

This error usually occurs because the ZIP signature at the beginning of the file is invalid.

Normal ZIPs always start with magic bytes:

```
50 4B 03 04
```

But after checking with `xxd` :

```bash
xxd zxcasdqwe.zip | head
```

found:

```
00000000: aabb ccaa bbcc ...
```

This means that the ZIP header has been deliberately damaged (corrupted).

---

## Completion

### 1. Fixed ZIP Header

Since only the initial signature is damaged, we can overwrite the first 4 bytes of the file with the correct ZIP signature.

```bash
printf '\x50\x4B\x03\x04' | dd of=zxcasdqwe.zip bs=1 seek=0 count=4 conv=notrunc
```

After that the file can be opened again.

---

### 2. ZIP File Extraction

Once the headers are fixed, run:

```bash
unzip zxcasdqwe.zip
```

The ZIP turns out to be password protected and provides a hint via the file name inside:

```
pass_nya_ada_di_metadata_zip_nya!
```

From this clue, the password is searched from the ZIP metadata.
After trying several possibilities, the correct password turned out to be the name of the ZIP file itself:

```
zxcasdqwe
```

---

### 3. Decode the Extracted File

ZIP produces a file:

```
pass_nya_ada_di_metadata_zip_nya!
```

The contents of the file are Base64 text which, if viewed, begins with:

```
iVBORw0KGgo...
```

This prefix indicates that the data is PNG in Base64 form.

Decode to PNG file:

```bash
cat pass_nya_ada_di_metadata_zip_nya\! | base64 -d > output.png
```

---

### 4. Scan the QR Code

After the image has been successfully created, the QR code in it is read using `zbarimg` :

```bash
zbarimg output.png
```

Output:

```
QR-Code:FGTE{REDACTED}
```

---

## Flags

```
FGTE{REDACTED}
```
