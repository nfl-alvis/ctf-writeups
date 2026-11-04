---
title: "scrambled"
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

**scrambled** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description
The challenge provides an image that looks damaged/random (*scrambled*). The pattern in the image shows that the file is not corrupt, but is the result of the process of **cutting the image into several tiles** and then rearranging the tiles in a certain order (*permutation*).

{{< image src="./images/scrambled.png" >}}

The objective of the challenge is to return the tile arrangement to its original position so that the image is readable again.

---

## Solution Steps

### 1. Identify the image size and grid pattern
From the image file properties, we get the size:

- Width : **1280**
- Height : **854**

The challenge uses a total of **16 tiles**, so the most common grid is **4×4**:

```
01 02 03 04
05 06 07 08
09 10 11 12
13 14 15 16
```
With this grid, tile sizes are calculated using integer division to be consistent with the scramble process:

```python
tw = w // 4
th = h // 4
```
---

### 2. Crop the image into 16 tiles

The image is then cut into 16 parts based on the row reading order (left to right, top to bottom).
Tiles are stored in memory so that there is no need to create output tile files one by one.

---

### 3. Determine permutation mapping

After observing the results of the image arrangement, the following mapping was obtained:

> **output_position → origin_tile**

```
01 -> 13
02 -> 15
03 -> 09
04 -> 01
05 -> 14
06 -> 16
07 -> 10
08 -> 02
09 -> 03
10 -> 11
11 -> 05
12 -> 07
13 -> 04
14 -> 12
15 -> 06
16 -> 08
```

If visualized in grid form:

```
13 15  9  1
14 16 10  2
 3 11  5  7
 4 12  6  8
```

This means, for example, at the output position `01` we have to paste the tile number `13` , at the output position `02` paste the tile number `15` , and so on.

---

### 4. Image reconstruction based on mapping

Once the tile is available, create a new canvas then paste the tile according to the mapping to the correct position, then save it as `recovered.png` .

---

## Script Solver (Python)

```python
from PIL import Image
import sys

pos_to_tile = {
    1: 13,  2: 15,  3: 9,   4: 1,
    5: 14,  6: 16,  7: 10,  8: 2,
    9: 3,   10: 11, 11: 5,  12: 7,
    13: 4,  14: 12, 15: 6,  16: 8
}

img = Image.open(sys.argv[1])
w, h = img.size

tw, th = w // 4, h // 4

tiles = {}
idx = 1
for r in range(4):
    for c in range(4):
        tiles[idx] = img.crop((c * tw, r * th, (c + 1) * tw, (r + 1) * th))
        idx += 1

out = Image.new("RGB", (tw * 4, th * 4))

for pos in range(1, 17):
    r, c = divmod(pos - 1, 4)
    out.paste(tiles[pos_to_tile[pos]], (c * tw, r * th))

out.save("recovered.png")
print("[+] saved recovered.png")
```

---

## How to Run

Install dependencies:

```bash
pip install pillow
```

Run solver:

```bash
python3 solve.py scrambled.png
```

The output will be saved as:

```
recovered.png
```

---

## Results

After the image is reconstructed, several candidate flags are visible in the final result.
According to the challenge format, only one flag is valid and can be selected as the answer.

{{< image src="./images/solved.png" >}}
