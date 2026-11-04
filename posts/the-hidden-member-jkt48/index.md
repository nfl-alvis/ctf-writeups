# TheHiddenMemberJKT48



{{< image src="./images/last-seen.webp" >}}

> **A seemingly normal portrait contains an elusive presence. Can you reconstruct the damaged boundaries and uncover the secrets within?**

{{< admonition tldr "TL;DR" >}}

**TheHiddenMemberJKT48** is an advanced steganography challenge that combines file-carver evasion with color distribution analysis. The provided image ( `Last_seen.jpg` ) contains two concatenated JPEG streams. The second image was deliberately modified by stripping the standard Start-of-Image ( `FF D8` ) and APP0 ( `FF E0` ) headers while corrupting the `JFIF` identifier to `JFIP` , defeating automated tools like `binwalk` and `foremost` . Manually reconstructing the missing headers and analyzing the extracted image with AperiSolve's color remapping filter reveals the hidden flag.

{{< /admonition >}}

## 1. Initial Reconnaissance & Structural Hex Analysis

We begin by examining the structure of the provided image file:

```bash
file Last_seen.jpg
stat -c "%s bytes" Last_seen.jpg
```

```text
Last_seen.jpg: JPEG image data, JFIF standard 1.01
226397 bytes
```

We search for End-of-Image (**EOI**) markers ( `FF D9` ) within the binary:

```bash
grep -oba $'\xff\xd9' Last_seen.jpg
```

Output:

```text
112763
226395
```

### Analysis of the Markers

- **Offset `112763` :** The first `FF D9` marker terminates the primary JPEG container approximately halfway through the file.
- **Offset `226395` :** The second `FF D9` marker appears at the exact end of the file ( `226397` total bytes minus 2 bytes of marker).

The presence of two distinct EOI markers confirms that two independent JPEG images are stored sequentially in a single file.

---

## 2. Investigating the Boundaries

We inspect the byte boundary directly following the first EOI marker ( `offset 112760` to `112792` ):

```bash
xxd -s 112760 -l 32 Last_seen.jpg
```

```text
0001b878: ffd9 aae8 0010 4a46 4950 0001 0101 0048  ......JFIP.....H
0001b888: 0048 0000 ffe1 007a 4578 6966 0000 4d4d  .H.....zExif..MM
```

### Key Observations

1. `FF D9` marks the end of the first image stream at offset `112763` .
2. Immediately following the marker, we see corrupted bytes ( `aa e8` ) followed by `00 10 4a 46 49 50` ( `JFIP` ).
3. The valid JPEG Start-of-Image (**SOI** `FF D8` ) and APP0 marker (** `FF E0` **) were purposefully stripped.
4. The standard `JFIF` magic string was altered to `JFIP` (hex `50` instead of `46` ).

This deliberate corruption prevents signature-based carving tools like `binwalk` and `foremost` from recognizing the embedded second image.

---

## 3. Reconstructing the Embedded Image

To restore the hidden image into a valid JPEG file, we need to:

1. Carve the raw payload from offset `112767` through the end of the file.
2. Synthesize the missing JPEG SOI header ( `FF D8` ) and standard APP0 header ( `FF E0 00 10 4A 46 49 46` ).

```bash
# Extract raw embedded stream starting after the corrupted SOI/APP0 bytes
dd if=Last_seen.jpg of=second_raw.bin bs=1 skip=112767

# Prepend a valid JPEG SOI + APP0 header
printf "\xff\xd8\xff\xe0\x00\x10\x4a\x46\x49\x46" > header.bin
cat header.bin second_raw.bin > hidden_member.jpg
```

We verify the repaired image with `file` and image viewers:

```bash
file hidden_member.jpg
```

```text
hidden_member.jpg: JPEG image data, JFIF standard 1.01, resolution (DPI), density 72x72, segment length 16, Exif Standard: [TIFF image data, big-endian]
```

The reconstructed image opens cleanly:

{{< image src="./images/hidden-member.webp" >}}

---

## 4. Color Distribution & Remapping Analysis

While the reconstructed image displays properly, no text or flag is visible to the naked eye.

We upload the image to **AperiSolve** for deep steganographic decomposition. Inspecting the **Color Remapping** layer reveals high-contrast luminescence patterns separating the background pixels:

Under the **Decomposer $\rightarrow$ Color Remapping** plane, the embedded text emerges clearly:

```text
FGTE{Th3_H1dd3n_M3mber_F0und}
```

---

## Flags

```text
FGTE{Th3_H1dd3n_M3mber_F0und}
```

---

## Key Takeaways

1. **Header Manipulation Defeats Naive Carvers:** Attackers or CTF authors can bypass standard file carving signatures simply by stripping the first few bytes (SOI/APP0) or modifying magic bytes ( `JFIF` $\rightarrow$ `JFIP` ).
2. **Boundary Analysis via EOI:** Searching for internal `FF D9` markers is an effective technique to spot concatenated JPEG payloads that automated tools miss.
3. **Multi-layer Steganography:** When an extracted image appears normal, color plane separation and histogram equalization (Color Remapping) can reveal subtle, single-channel pixel shifts.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/the-hidden-member-jkt48/  

