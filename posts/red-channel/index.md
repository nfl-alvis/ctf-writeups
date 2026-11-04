# Red Channel




{{< admonition tldr "TL;DR" >}}

**Red Channel** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Challenge Description
The Challenge provides a ZIP file containing multiple sequential PNG images ( `img_000.png` to `img_055.png` ).  
The title of the challenge refers to steganography techniques using **red channel**.

---

## Files
- `RedChannel.zip` - `img_000.png` … `img_055.png` ---

## Solution Steps

### 1. Extract ZIP
First, extract the archive To view all available files.

```bash
unzip RedChannel.zip
ls
```
The result is dozens of PNG files with the naming pattern `img_XXX.png` .

---

### 2. Initial Analysis of File Patterns

The number of images arranged sequentially usually indicates:

* each image stores a portion of the message (e.g. 1 character), or
* messages are spread on certain channels (RGB).

Because the challenge title is **Red Channel**, the focus of the analysis is directed to the **Red (R)** channel.

---

### 3. Verify with StegSolve

To ensure that the data is indeed inserted in the red channel, one of the images is opened using StegSolve:

```bash
java -jar stegsolve.jar img_000.png
```

Then use the features:

* **Data Extract**
* check **Red bit-plane 0 to 7**
* Extract By: **Row**
* Bit Order: **MSB First**

The preview results show the hex byte `46` repeated.

---

### 4. Convert Hex Values to ASCII

The hex value `0x46` is the ASCII character:

* `0x46` → `F` When checked on the next image, the characters change sequentially:

* `img_001.png` → `G` * `img_002.png` → `T` * `img_003.png` → `E` * `img_004.png` → `{` So it can be concluded that:

* each image stores **1 character**
* Characters are stored in **Red channel**

---

### 5. Extraction Automation with Python

To avoid having to open all images manually, extraction was performed using Python.

Because the entire 0..7 bit-plane in the red channel is used, the character can be taken directly from the Red byte value at a particular pixel (for example `(0,0)` ).

```python
from PIL import Image
import glob

files = sorted(glob.glob("img_*.png"))

flag = ""
for f in files:
    im = Image.open(f).convert("RGB")
    r, _, _ = im.getpixel((0, 0))
    flag += chr(r)

print(flag)
```

---

## Results

After the script is run, the complete flag output is obtained:

{{< image src="./images/solved.png" >}}


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/red-channel/  

