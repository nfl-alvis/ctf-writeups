---
title: "nothing"
date: 2026-02-22
categories:
  - Misc
tags:
  - Misc
  - FGTE
collections:
  - FGTE
---


{{< admonition tldr "TL;DR" >}}

**nothing** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

Challenge provides a YouTube video link that looks “blank”:

- black screen for a few seconds
- no audio
- no clear visual information

Links:
- `https://www.youtube.com/watch?v=rBDToOhEr6c`

---

## Solution Steps

### 1. Checking Video Subtitles

The first step is to open the video and check whether the video has subtitles.

{{< image src="./images/b64.png" >}}

It turns out that the video has subtitles, and when the subtitles are changed to **Russian** , a string appears that is not normal text, but looks like **Base64**.

---

### 2. Decode Base64

The Base64 string is then decoded.

The decode results produce a link to **Pastebin**, but Pastebin is locked and requires a password.

---

### 3. Look for the password in the video description

Because Pastebin requires a password, The next step is to check other parts of the video.
In the video description, another ciphertext is found which looks like the result of **Bacon Cipher** (Baconian cipher), namely a cipher that encodes text using the A/B pattern.

{{< image src="./images/bacon.png" >}}

---

### 4. Decode Bacon Cipher

The ciphertext in the description is decoded using the Bacon cipher technique.

The plaintext results from this decoding produce a word/sentence which is strongly suspected to be the password for Pastebin.

---

### 5. Open Pastebin with Password

The password from the Bacon cipher decode is entered into the Pastebin page.
Once the password is correct, the contents of Pastebin will open and contain a challenge flag.

{{< image src="./images/solved.png" >}}

---

## Flags

```
FGTE{REDACTED}
```
