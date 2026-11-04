---
title: "Corgi Can Fly"
date: 2026-02-22
categories:
  - Misc
tags:
  - Misc
  - ctf.hackme.quest
collections:
  - ctf.hackme.quest
---



{{< admonition tldr "TL;DR" >}}

**Corgi Can Fly** is a misc challenge from **ctf.hackme.quest**. Corgis are cute, right?      [Pillow (Python)](https://pillow.readthedocs.io/) and [Bitmap (.NET)](<https://msdn.microsoft.com/zh-tw/library/system.drawing.bitmap(v=vs.110).aspx>) are your friends.      (Maybe you can try `stegsolve` )

{{< /admonition >}}

## Challenge Description

Corgis are cute, right?  
  
[Pillow (Python)](https://pillow.readthedocs.io/) and [Bitmap (.NET)](<https://msdn.microsoft.com/zh-tw/library/system.drawing.bitmap(v=vs.110).aspx>) are your friends.  
  
(Maybe you can try `stegsolve` )

---

## Technical Analysis

We are provided with an image file named:

- `corgi-can-fly.png`

{{< image src="./images/corgi-can-fly.png" >}}

The first step is to carry out an inspection using **StegSolve** to look for possible hidden data in the color channel.

Upon opening the image using `stegsolve` , a QR Code was found hidden in the menu:

- **Red Plane 0**

{{< image src="./images/solved.png" >}}

The QR Code is then scanned to obtain a flag.

---

##Flags `FLAG{Corgi is cutest aniaml on the earth >////////<}`
