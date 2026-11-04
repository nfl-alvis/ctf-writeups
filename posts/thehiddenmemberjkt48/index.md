# TheHiddenMemberJKT48 — Steganography Writeup



{{< admonition tldr "TL;DR" >}}

**TheHiddenMemberJKT48 — Steganography Writeup** is a forensics challenge from **FGTE**. Here's a professional writeup in Markdown format. The language is created like a systematic and analytical CTF player.

{{< /admonition >}}

Here's a professional writeup in Markdown format.
The language is created like a systematic and analytical CTF player.

---

#TheHiddenMemberJKT48 — Steganography Writeup

## Challenge Description

We are provided with an image file:

```
Last_seen.jpg
```

The name of the challenge **“TheHiddenMemberJKT48”** indicates that there is something hidden in the image.

---

## 1. Initial Recon

The first step is to check the JPEG file structure.

```bash
xxd Last_seen.jpg | head
```

Found standard headers:

```
FF D8 FF E0 ... JFIF
```

This means the file is a normal JPEG.

However After performing the EOI marker search:

```bash
grep -oba $'\xff\xd9' Last_seen.jpg
```

Found:

```
112763
226395
```

File size:

```bash
stat -c %s Last_seen.jpg
```

```
226397
```

## Technical Analysis

* `112763` → First End Of Image
* `226395` → Second End Of Image
* File size ≈ 226397 bytes

This indicates the presence of **two JPEG structures in one file**.

---

## 2. Second Offset Investigation

After checking the offset around the first EOI:

```bash
xxd -s 112760 -l 32 Last_seen.jpg
```

Output:

```
... ff d9 aa e8 00 10 4a 46 49 50 ...
```

Visible:

* `FF D9` → end of first JPEG
* After that `JFIP` appears (not `JFIF` )
* Neither `FF D8` nor `FF E0` found 

### Conclusion

The second JPEG was intentionally modified:

* `FFD8` (SOI) removed
* `FFE0` (APP0) removed
* `JFIF` changed to `JFIP` This technique aims to:

* Not detected by `foremost` * Not detected by `binwalk` * Cannot be carved automatically

---

## 3. Second JPEG Reconstruction

Because the initial marker was removed, manual reconstruction was performed.

## Methodology:

1. Determine a valid starting offset ( `112767` )
2. Extract up to the second EOI
3. Add manual JPEG header
4. Fix `JFIP` → `JFIF` After successful reconstruction, a second valid JPEG file is obtained.

However, the image **does not immediately display the flag**.

---

## 4. Advanced Analytics — AperiSolve

The reconstructed files are then analyzed using:

🔎 **AperiSolve**

The analysis results show the presence of suspicious artifacts in:

> **Decomposer → Color Remapping**

---

## 5. Decomposer – Color Remapping

In the **Color Remapping** section, visual patterns can be seen that were not previously visible in normal images.

This technique works by:

* Separating color components
* Remapping color distribution
* Highlights hidden intensity differences

The result displays hidden text in the form of a flag.

---

## 6. Flags

Flag was successfully obtained From the result of **Color Remapping** analysis in AperiSolve.

```
FLAG{...}
```

---

# Conclusion

This challenge combines two techniques:

1. **Embedded JPEG with defaced header**

   * Avoid signature-based carving
2. **Color-based steganography**

   * Flag hidden through manipulation of color distribution

Approach used:

* Manual JPEG structure analysis
* Identify multiple EOIs
* Header reconstruction
* Advanced visual analysis using AperiSolve

This challenge tests:

* Understanding of JPEG file structure
* Hex analysis capabilities
* Ability to differentiate automatic vs manual carving files
* Awareness of color-based steganography techniques

---

If you want, I can make a writeup version that is more "CTF competitive style" (more concise, sharp, and straight to the point like a big team writeup).


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/thehiddenmemberjkt48/  

