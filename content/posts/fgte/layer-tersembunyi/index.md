---
title: "layer tersembunyi"
date: 2026-02-22
categories:
  - Forensics
tags:
  - Forensics
  - FGTE
collections:
  - FGTE
---



{{< admonition tldr "TL;DR" >}}

**hidden layer** is a forensics challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

We are provided with an image file named `cat.png` .

Visually, when the image is opened using a regular image viewer, there are no visible irregularities or hidden information.

{{< image src="./images/cat.png" >}}

There is no text, suspicious distortion, or visual artifacts to indicate the presence of a flag.

---

## Technical Analysis and Solution

Because no visible abnormalities were found, it is likely that the flag was hidden using **color channel-based steganography** techniques.

### 1. Analysis Using Stegsolve

The image is then analyzed using the tool [Stegsolve](http://www.caesum.com/handbook/Stegsolve.jar), which allows exploration:

* Bitplane
* Color channels (Red, Green, Blue)
* Combination of layers

```
java -jar ~/bin/stegsolve.jar cat.png
```

After performing checks on various channels and bit planes, it was found that:

```
Green Plane 0
```

there is clearly visible flag text.

{{< image src="./images/solved.png" >}}

This layer displays hidden information that is not visible in the normal view of the image.

---

##Flags

```
FGTE{Redacted}
```
