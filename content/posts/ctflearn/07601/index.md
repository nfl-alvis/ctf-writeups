---
title: "07601"
date: 2026-02-22
categories:
  - Forensics
tags:
  - Forensics
  - CTFlearn
collections:
  - CTFlearn
---



{{< admonition tldr "TL;DR" >}}

**07601** is a forensics challenge from **CTFlearn**. ---

{{< /admonition >}}

---

## Description

I think I lost my flag there. Hopefully, it won't get attacked...
[AGT.png](https://mega.nz/#!CXYXBQAK!6eLJSXvAfGnemqWpNbLQtOHBvtkCzA7-zycVjhHPYQQ)

---

## Technical Analysis 

We are provided with the image file `AGT.png` :

{{< image src="./images/AGT.png" >}}

The `AGT.png` file appears as a regular image file. However, to ensure the actual format, identify the file type using the command file.

```bash
file AGT.png
```

Output:

```
AGT.png: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 300x168, components 3
```

From the results it is known that even though it has the extension `.png` , this file is actually in **JPEG** format. Therefore, file extensions are adjusted to be consistent with the actual format.

```bash
cp AGT.png AGT.jpeg
```

---

## Hexadecimal Technical Analysis

The next step is to analyze the contents of the file at the hexadecimal level to look for indications of hidden data.

```bash
xxd AGT.jpeg | tail
```

Output:

```
0006a570: 2f55 5808 002c fc15 582c fc15 5850 4b01  /UX..,..X,..XPK.
0006a580: 0215 0314 0008 0008 0009 4f5e 4970 1a8d  ..........O^Ip..
0006a590: c05d 0000 00ab 0000 003f 000c 0000 0000  .].......?......
0006a5a0: 0000 0000 4080 8137 2502 005f 5f4d 4143  ....@..7%..__MAC
0006a5b0: 4f53 582f 5365 6372 6574 2053 7475 6666  OSX/Secret Stuff
0006a5c0: 2e2e 2e2f 446f 6e27 7420 4f70 656e 2054  .../Don't Open T
0006a5d0: 6869 732e 2e2e 2f2e 5f49 2057 6172 6e65  his.../._I Warne
0006a5e0: 6420 596f 752e 6a70 6567 5558 0800 03fc  d You.jpegUX....
0006a5f0: 1558 02fc 1558 504b 0506 0000 0000 0900  .X...XPK........
0006a600: 0900 4303 0000 1126 0200 0000            ..C....&....
```

The output indicates the presence of a suspicious _signature_, such as `PK` which is the _signature_ file **_ZIP Archive_**, and also looks like a path like `__MACOSX/Secret Stuff...` This indicates that there are several files embedded in the image.

---

## Hidden File Extraction

To extract the hidden files, use `binwalk` :

```bash
binwalk -e AGT.jpeg
```

The extraction results produce the following file structure:

```
.
├── 25118.zip
├── 2570.zip
├── 47CA2.zip
├── I Warned You.jpeg
├── __MACOSX
│   └── Secret Stuff...
│       └── Don't Open This...
└── Secret Stuff...
    └── Don't Open This...
        └── I Warned You.jpeg
```

## Flag Search

One of the extracted files is the `I Warned You.jpeg` image. To look for possible hidden _flags_ in it, a string search is performed using `strings` .

```bash
strings "I Warned You.jpeg" | grep -i "CTF"
```

Output:

```
ABCTF{Du$t1nS_D0jo}
```

---

##Flags

```ABCTF{Du$t1nS_D0jo}```
