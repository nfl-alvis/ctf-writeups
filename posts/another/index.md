# Another




{{< admonition tldr "TL;DR" >}}

**another** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description
This challenge provides a normal-looking `chall.png` image file. However, according to the description of the problem, there is secret information inserted in the pixel using steganography techniques.

The given hint is `zsteg` , so the main approach is to perform [bit-plane](https://en.wikipedia.org/wiki/Bit_plane?_x_tr_sl=en) analysis on the image to look for hidden data.

---

## Solution Steps

### 1. Image Analysis Using zsteg
As per the hint, The first step is to run `zsteg` on the image file.

```bash
zsteg chall.png
```
The `zsteg` tool scans various combinations of channels (RGB), bit-orders (LSB/MSB), and reading methods (xy).

The output indicates the presence of a string that clearly contains the flag:

```
b1,r,lsb,xy         .. text: "FGTE{Redacted}"
```

---

##Flags

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/another/  

