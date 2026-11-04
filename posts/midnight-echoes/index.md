# Midnight Echoes




{{< admonition tldr "TL;DR" >}}

**Midnight Echoes** is a forensics challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

We are provided with an image file named `Midnight_Echo.png` .
It is known that hidden in the image is a **ZIP** file, which after extracting contains the audio file `mp3` .

Our task is to extract hidden information from the MP3 file using the tool [MP3Stego](https://www.petitcolas.net/fabien/software/MP3Stego_1_1_19.zip).

---

## Technical Analysis and Solution

### 1. Identify Hidden Data in Images

The first step is to check whether there is additional data in the PNG file using `zsteg` .

```bash
zsteg Midnight_Echo.png
```

An indication of additional data after the final PNG marker ( `IEND` ) was found:

```
243606 bytes of extra data after image end (IEND)
```

This is a strong indication that there is an inserted file (appended file).

Additionally found suspicious metadata:

```
meta Comment text: "neonwaves"
```

The string `neonwaves` is thought to be an important clue, possibly a password.

---

### 2. ZIP File Extraction

To extract hidden files, use the `foremost` tool:

```bash
foremost Midnight_Echo.png
```

Obtained files:

```
00005522.zip
```

When trying to extract the ZIP file, it asks for a password.
Using the previous instructions, use the password:

```
neonwaves
```

The password is correct, and the file has been successfully extracted:

```
Midnight_Echo.mp3
```

---

### 3. Extraction of Hidden Data in MP3

The next step is to analyze the MP3 file using **Mp3Stego**.

Commands used:

```bash
./Decode.exe -X -P neonwaves ~/CTF/FGTE/Steganography/Midnight\ Echoes/Midnight_Echo.mp3
```

Parameters used:

* `-X` → Extract hidden messages
* `-P neonwaves` → Password

The decoding process runs successfully, producing the output file:

```
Midnight_Echo.mp3.txt
```

---

### 4. Get the Flag

After Opening the file `Midnight_Echo.mp3.txt` , the flag was discovered in it.

{{< image src="./images/solved.png" >}}

---

##Flags

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/midnight-echoes/  

