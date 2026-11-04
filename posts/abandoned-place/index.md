# Abandoned Place




{{< admonition tldr "TL;DR" >}}

**Abandoned Place** is a forensics challenge from **CTFlearn**. the flag is outside of the pic, try to find it.   another hint: dimensions, dimensions, everything is in dimensions.

{{< /admonition >}}

##Challenge

the flag is outside of the pic, try to find it.  
another hint: dimensions, dimensions, everything is in dimensions.

Challenge files:  
[abondoned_street_challenge2.jpg](https://ctflearn.com/challenge/download/1000)

---

## Technical Analysis

We are provided with an image file named `abondoned_street_challenge2.jpg` .  
When opened normally, nothing suspicious was found visually.

{{< image src="./images/abondoned_street_challenge2.jpg" >}}

However, based on the hint given, namely **"dimensions"**, it can be assumed that the hidden data is outside the area of the image being displayed. A common technique in challenges like this is to **manipulate image dimension metadata** (width/height), so that some hidden pixels are not rendered by normal viewers.

In other words: **the image file contains more pixels than it displays**, and we need to change the dimensions so that all pixel data can be seen.

---

## Exploitation / Solution

To change the image dimensions, I used the Python script `modsize.py` available on GitHub:

- Script reference: https://github.com/flawwan/modsize/blob/master/modsize.py

The script works by:
1. Read image files in bytes.
2. Find the header offset that stores the width and height values.
3. Change the value to the new dimension.
4. Save the results as an output file.

Scripts used:

```python
import argparse
import os
from subprocess import call

import filetype
from loguru import logger

rot_value = 0  # Default value
parser = argparse.ArgumentParser()
parser.add_argument("file", help="Filepath to image")
parser.add_argument("output", help="Filepath to image")
parser.add_argument("--width", "-sw", type=int, help="New width of image")
parser.add_argument("--height", "-sh", type=int, help="New height of image")
args = parser.parse_args()

def modify_file(offset1, offset2, filename, output, width=None, height=None):
    logger.info("Loading image")
    bin_arr = []
    with open(filename, "rb") as f:
        arr = f.read()
    for b in arr:
        bin_arr.append(b)

    org_width = hex(int(hex(bin_arr[offset1])[2:] + hex(bin_arr[offset1 + 1])[2:], 16))
    org_height = hex(int(hex(bin_arr[offset2])[2:] + hex(bin_arr[offset2 + 1])[2:], 16))

    logger.info("Image loaded!")
    logger.info("Detected width: %d px" % int(org_width, 16))
    logger.info("Detected height: %d px" % int(org_height, 16))

    if width is None and height is None:
        logger.warning("Nothing todo. Set width/height?")
        exit()

    if width is None:
        width = int(org_width, 16)
    if height is None:
        height = int(org_height, 16)

    new_width = str(hex(width))[2:].zfill(4)
    new_height = str(hex(height))[2:].zfill(4)

    if str(org_width)[2:] != new_width:
        logger.info("New width: %d px" % int(new_width, 16))
    if str(org_height)[2:] != new_height:
        logger.info("New height: %d px" % int(new_height, 16))

    # Set width
    bin_arr[offset1] = int(str(new_width)[:2], 16)
    bin_arr[offset1 + 1] = int(str(new_width)[2:], 16)

    # Set height
    bin_arr[offset2] = int(str(new_height)[:2], 16)
    bin_arr[offset2 + 1] = int(str(new_height)[2:], 16)

    logger.info("Saving new image file")
    with open(output, "wb") as binary_file:
        binary_file.write(b"".join([(x).to_bytes(1, "big") for x in bin_arr]))
    logger.info("Image saved!")

def modify_png(filename, output, width, height):
    modify_file(18, 22, filename, output, width, height)

    # Fix crc32 checksum
    logger.info("Fixing checksum of new image")
    call(["pngcsum", "%s" % output, output + "new"])
    logger.info("Checksum now OK")

    os.remove("%s" % output)
    os.rename("%snew" % output, output)

def modify_jpg(filename, output, width, height):
    bin_arr = []
    with open(filename, "rb") as f:
        arr = f.read()
    prev = ""
    i = 0
    for b in arr:
        if prev + hex(b)[2:] == "ffc0":
            break
        i += 1
        prev = hex(b)[2:]
    logger.info("Found magic bytes on offset %d " % i)
    modify_file(i + 6, i + 4, filename, output, width, height)

def process_file(filename, output, width, height):
    kind = filetype.guess(filename)
    if kind is None:
        print("Filetype not supported!")
        return
    if kind.mime == "image/png":
        logger.info("Detected: png")
        modify_png(filename, output, width, height)
    elif kind.mime == "image/jpeg":
        logger.info("Detected: jpg")
        modify_jpg(filename, output, width, height)
    else:
        logger.info("Filetype not supported")

process_file(args.file, args.output, args.width, args.height)
```
---

## Execute Script

I changed the image dimensions to:

* Width: `2016` * Height: `1120` Commands used:

```bash
python3 modsize.py --width 2016 --height 1120 abondoned_street_challenge2.jpg outpng.png
```

After the output file is opened ( `outpng.png` ), the previously hidden part of the image is visible, and a flag appears in the additional area.

{{< image src="./images/outpng.png" >}}

---

##Flags

```
urban_exploration
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/abandoned-place/  

