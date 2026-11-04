# Video QR



{{< admonition tldr "TL;DR" >}}

**Video QR** is a misc challenge from **FGTE**. The QR output format looks like:

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Challenge Description
The challenge provides a video file ( `video.mp4` ) containing many QR codes.  
Each QR code does not contain a direct flag, but pieces of data that must be collected and reassembled.

The QR output format looks like:

```
IDX:000/67|<base64>
IDX:001/67|<base64>
...
```
This means that each QR carries 1 chunk of base64 data, with index `IDX` and total chunks `/67` .

---

##Solution

### 1) Extract frames from video
The first step is to break the video into a collection of images so that each QR code can be processed per frame.

```bash
ffmpeg -i video.mp4 frames/frame_%05d.png
```
---

### 2) Decode QR of each frame

After the frames are successfully extracted, each image is read using OpenCV and then decoded with `pyzbar` .

To make the QR easier to read, the frame is upscaled and then converted to grayscale and threshold before processing.

Scripts:

```python
import cv2
import glob
from pyzbar.pyzbar import decode

frames = sorted(glob.glob("frames/*.png"))

seen = set()
results = []

for f in frames:
    img = cv2.imread(f)
    if img is None:
        continue

    img = cv2.resize(img, None, fx=3, fy=3, interpolation=cv2.INTER_CUBIC)

    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    _, th = cv2.threshold(gray, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)

    qr = decode(th)
    if not qr:
        continue

    data = qr[0].data.decode(errors="ignore")

    if data not in seen:
        seen.add(data)
        results.append(data)
        print(data)

print("\nTOTAL UNIQUE:", len(results))
```

The output is saved:

```bash
python3 decode_qr.py > output.txt
```

---

### 3) Rebuild base64 data from all chunks

The contents of `output.txt` contain a QR snippet in the format:

```
IDX:000/67|...
```

The next step is to:

* parse all chunks
* mapping `IDX -> chunk` * reorder from `0..66` * combine base64
* decode base64 into image file ( `recovered.jpg` )

Scripts:

```python
import re
import base64

data = open("output.txt", "r", encoding="utf-8", errors="ignore").read()
parts = re.findall(r"IDX:(\d{1,3})/(\d+)\|([A-Za-z0-9+/=]+)", data)

total_expected = int(parts[0][1])

mp = {}
for idx, total, chunk in parts:
    mp[int(idx)] = chunk

missing = [i for i in range(total_expected) if i not in mp]

print("Expected:", total_expected)
print("Got:", len(mp))
print("Missing:", missing)

b64 = "".join(mp[i] for i in range(total_expected))
b64 += "=" * ((4 - len(b64) % 4) % 4)

raw = base64.b64decode(b64)
open("recovered.jpg", "wb").write(raw)

print("Saved -> recovered.jpg")
```

When executed, all chunks are successfully collected:

```
Expected: 67
Got: 67
Missing: []
Saved -> recovered.jpg
```

---

### 4) Decode flags from last base64

When opening the file `recovered.jpg` there is a base64 encoded ciphertext which, if decoded, will produce a flag

{{< image src="./images/recovered.jpg" >}}

---

## Flags

```
FGTE{REDACTED}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/video-qr/  

