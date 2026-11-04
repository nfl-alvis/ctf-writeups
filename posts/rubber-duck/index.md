# Rubber Duck




{{< admonition tldr "TL;DR" >}}

**Rubber Duck** is a forensics challenge from **CTFlearn**. ---

{{< /admonition >}}

---

## Description

Find the flag! Simple forensics challenge to get started with.
[RubberDuck.jpg](https://ctflearn.com/challenge/download/933)

---

## Technical Analysis

We are provided with the image file `RubberDuck.jpg` :

{{< image src="./images/RubberDuck.jpg" >}}

Check the metadata on the file with the `exiftool` utility:

```bash
exiftool RubberDuck.jpg
```

Output:

```
ExifTool Version Number         : 13.25
File Name                       : RubberDuck.jpg
Directory                       : .
File Size                       : 197 kB
File Modification Date/Time     : 2026:01:07 19:59:40+07:00
File Access Date/Time           : 2026:01:07 19:59:43+07:00
File Inode Change Date/Time     : 2026:01:07 19:59:43+07:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 72
Y Resolution                    : 72
Comment                         : CTFlearn{ILoveJakarta}.
Profile CMM Type                : Apple Computer Inc.
Profile Version                 : 4.0.0
Profile Class                   : Display Device Profile
Color Space Data                : RGB
Profile Connection Space        : XYZ
Profile Date Time               : 2017:07:07 13:22:32
Profile File Signature          : acsp
Primary Platform                : Apple Computer Inc.
CMM Flags                       : Not Embedded, Independent
Device Manufacturer             : Apple Computer Inc.
Device Model                    :
Device Attributes               : Reflective, Glossy, Positive, Color
Rendering Intent                : Perceptual
Connection Space Illuminant     : 0.9642 1 0.82491
Profile Creator                 : Apple Computer Inc.
Profile ID                      : ca1a9582257f104d389913d5d1ea1582
Profile Description             : Display P3
Profile Copyright               : Copyright Apple Inc., 2017
Media White Point               : 0.95045 1 1.08905
Red Matrix Column               : 0.51512 0.2412 -0.00105
Green Matrix Column             : 0.29198 0.69225 0.04189
Blue Matrix Column              : 0.1571 0.06657 0.78407
Red Tone Reproduction Curve     : (Binary data 32 bytes, use -b option to extract)
Chromatic Adaptation            : 1.04788 0.02292 -0.0502 0.02959 0.99048 -0.01706 -0.00923 0.01508 0.75168
Blue Tone Reproduction Curve    : (Binary data 32 bytes, use -b option to extract)
Green Tone Reproduction Curve   : (Binary data 32 bytes, use -b option to extract)
Image Width                     : 1536
Image Height                    : 2048
Encoding Process                : Progressive DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 1536x2048
Megapixels                      : 3.1
```

Found a flag in the `Comment : ___FL0X___` section.

---

##Flags

```CTFlearn{ILoveJakarta}```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/rubber-duck/  

