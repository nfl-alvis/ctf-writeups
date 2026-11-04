# Info Lokasi




{{< admonition tldr "TL;DR" >}}

**Location Info** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description
This challenge provides a `coords.txt` file containing the `(x, y)` coordinate set. The objective of the challenge is to visualize these coordinates so that they form a pattern/text that can be read as a flag.

---

## Solution Steps

### 1. Reading and Plotting Coordinates in Python
Since the file contains pairs of coordinates, the approach used is:
1. Read each line `x,y` 2. Save the value into a list
3. Plotting using `scatter()` Code used:

```python
import matplotlib.pyplot as plt

def load_coords(filename):
    xs, ys = [], []
    with open(filename, "r") as f:
        for line in f:
            line = line.strip()
            if not line or "," not in line:
                continue
            x, y = line.split(",")
            xs.append(int(x))
            ys.append(int(y))
    return xs, ys

xs, ys = load_coords("coords.txt")

plt.figure(figsize=(12, 6))
plt.scatter(xs, ys, s=5)

plt.gca().invert_yaxis()

plt.axis("equal")
plt.grid(True, linewidth=0.3)
plt.title("Plot Koordinat dari coords.txt")
plt.show()
```
---

### 2. Running the Script

After the script is executed, the coordinate points form text that can be read clearly.

The visualization results show the flags:

{{< image src="./images/solved.png" >}}

---

##Flags

```
FGTE{REDACTED}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/info-lokasi/  

