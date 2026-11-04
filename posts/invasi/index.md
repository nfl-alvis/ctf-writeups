# Invasi




{{< admonition tldr "TL;DR" >}}

**invasion** is a forensics challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

In this challenge We are provided with an audio file named `invasi.wav` . After further analysis, it was discovered that the audio was an SSTV (Slow-Scan Television) signal which needed to be decoded to obtain an image containing the flag.

---

## Initial Analysis

### 1. Identify File Characteristics

The first step is to perform an initial analysis of the `invasi.wav` file.

Several approaches taken:

* Play audio files directly. The audio sounds like a series of tones with varying frequencies, not a human voice or regular music.
* Opening the file using Sonic Visualiser To view the frequency spectrum.

In Sonic Visualiser, the frequency pattern looks stable and structured. However, no hidden metadata or strange data was found in the file header when checked with standard tools such as `file` or `strings` .

From the sound characteristics that resemble radio transmissions and the distinctive spectrum pattern, it can be assumed that the file is an SSTV signal.

---

## SSTV Brief Concept

SSTV (Slow-Scan Television) is a method of transmitting images via audio waves, usually used in amateur radio communications. The image data is converted into frequency variations which can then be reconstructed back into an image using an appropriate decoder.

Since the file is of the form `.wav` and has a consistent pitch pattern, it is likely that the audio was intended to be decoded as SSTV.

---

## Decoding Process

After identifying that the file is an SSTV signal, the next step is to decode it.

In this solution an online decoder is used:
[https://sstv-decoder.mathieurenaud.fr/](https://sstv-decoder.mathieurenaud.fr/)

Steps:

1. Upload the `invasi.wav` file to the site.
2. Let the system detect SSTV mode automatically.
3. The decoding results display an image.

The resulting image contains a challenge flag.

{{< image src="./images/solved.png" >}}

---

##Flags:

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/invasi/  

