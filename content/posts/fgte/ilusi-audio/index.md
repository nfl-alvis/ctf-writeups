---
title: "Ilusi Audio"
date: 2026-02-24
categories:
  - Forensics
tags:
  - Forensics
  - FGTE
collections:
  - FGTE
---



{{< admonition tldr "TL;DR" >}}

**Audio Illusion** is a forensics challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

We are provided with an audio file called `ilusi_audio.wav` which when played only produces strange and meaningless sounds.

The question description provides clues:

> “Don't just listen... look at the sound.”

These instructions lead to the **spectrogram** analysis technique, which is a visual representation of a signal's frequency spectrum over time. In many steganography category CTF challenges, messages are often hidden in the frequency domain and can only be seen through spectral visualization.

---

## Solution Steps

### 1. Initial Analysis

The `ilusi_audio.wav` file is played using a regular media player. Audio sounds like noise or a combination of high frequencies that do not form clear words. This indicates the possibility of data embedding in the frequency domain.

### 2. Spectrogram Visualization

To view the visual representation, use the **Sonic Visualiser** application.

Steps:

1. Open Sonic Visualiser.
2. Import file `ilusi_audio.wav` .
3. Add a spectrogram layer via:
   * `Layer` → `Add Spectrogram` ### 3. Message Identification

After visualizing it in the form of a spectrogram, certain patterns can be seen in the medium to high frequency range. This pattern forms text that is clearly read as a challenge flag. This message cannot be heard directly, but can be seen clearly on the spectrogram display.

{{< image src="./images/solved.png" >}}

---

##Flags

```FGTE{Redacted}```
