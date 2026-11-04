---
title: "WOW.... So Meta"
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

**WOW.... So Meta** is a forensics challenge from **CTFlearn**. This photo was taken by our target. See what you can find out about him from it. [3UWLBAUCb9Z2.jpg](https://mega.nz/#!ifA2QAwQ!WF-S-MtWHugj8lx1QanGG7V91R-S1ng7dDRSV25iFbk)

{{< /admonition >}}

## Description

This photo was taken by our target. See what you can find out about him from it. [3UWLBAUCb9Z2.jpg](https://mega.nz/#!ifA2QAwQ!WF-S-MtWHugj8lx1QanGG7V91R-S1ng7dDRSV25iFbk)

---

## Technical Analysis

We are provided with the image file `3UWLBAUCb9Z2.jpg` , the title of this challenge gives us a hint, namely `Metadata` View the photo's metadata with `exiftool` :

```bash
exiftool 3UWLBAUCb9Z2.jpg
```

Output:

```
ExifTool Version Number         : 13.25
File Name                       : 3UWLBAUCb9Z2.jpg
Directory                       : .
File Size                       : 104 kB
File Modification Date/Time     : 2026:01:06 21:58:04+07:00
File Access Date/Time           : 2026:01:06 21:58:23+07:00
File Inode Change Date/Time     : 2026:01:06 21:59:19+07:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Current IPTC Digest             : dbad0204d16a63027791298bc460859a
Coded Character Set             : UTF8
Application Record Version      : 2
Digital Creation Time           : 16:45:55
Digital Creation Date           : 2014:12:27
Time Created                    : 16:45:55
IPTC Digest                     : dbad0204d16a63027791298bc460859a
Exif Byte Order                 : Big-endian (Motorola, MM)
Orientation                     : Horizontal (normal)
X Resolution                    : 72
Y Resolution                    : 72
Resolution Unit                 : inches
Software                        : Photos 1.5
Modify Date                     : 2014:12:27 16:45:55
Exif Version                    : 0221
Date/Time Original              : 2014:12:27 16:45:55
Create Date                     : 2014:12:27 16:45:55
Components Configuration        : Y, Cb, Cr, -
Light Source                    : Tungsten (Incandescent)
Flashpix Version                : 0100
Color Space                     : sRGB
Exif Image Width                : 4002
Exif Image Height               : 1536
Scene Capture Type              : Standard
Sharpness                       : Hard
Padding                         : (Binary data 2060 bytes, use -b option to extract)
XMP Toolkit                     : XMP Core 5.4.0
Creator Tool                    : Photos 1.5
Date Created                    : 2014:12:27 16:45:55
Warning                         : [minor] Fixed incorrect URI for xmlns:MicrosoftPhoto
Camera Serial Number            : flag{EEe_x_I_FFf}
Image Width                     : 800
Image Height                    : 307
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 800x307
Megapixels                      : 0.246
Date/Time Created               : 2014:12:27 16:45:55
Digital Creation Date/Time      : 2014:12:27 16:45:55
```

From the output above the flag was discovered, namely: `___FL0X___` ---

##Flags

```flag{EEe_x_I_FFf}```
