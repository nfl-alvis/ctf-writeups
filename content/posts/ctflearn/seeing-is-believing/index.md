---
title: "Seeing is believing"
date: 2026-02-22
categories:
  - Forensics
tags:
  - Forensics
  - CTFlearn
collections:
  - CTFlearn
---



{{< admonition tldr "TL;DR" >}}

**Seeing is believing** is a forensics challenge from **CTFlearn**. This writeup provides a complete walkthrough and technical analysis.

{{< /admonition >}}

My colleague's an astronaut who's currently on a mission orbiting in space. Just a few hours ago, unfortunately, his communication device caught fire so he's unable to report back to base. I did, however, receive a strange file that I couldn't seem to open. I think it may shed some light on his situation.
Can you help me save poor boy Johnny? File: [help.me](https://mega.nz/#!LTRUTaZb!9Nh0NwDONJQiOThif3G62evP8H_W9eIJSu0PdBQWKyg)

---

## Technical Analysis

We are provided with the archive file `message.zip` trying to extract it getting :

```
Archive:  message.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
    68172  2018-01-06 22:25   seeingisbelieving/help.me
---------                     -------
    68172                     1 file
```

Try to see the `help.me` file type, check it with the command:

```file help.me```

Output:

```
Ogg data, Vorbis audio, mono, 44100 Hz, ~110000 bps, created by: Xiph.Org libVorbis I (1.3.3)
```

Know that it is an audio file `.ogg` , try changing the format with the command `cp` :

```
cp help.me help.ogg
```

Tried to open and listen to the contents of the file, the sound seemed damaged.
Tried to view the spectrogram on the `Sonic Visualiser` application, found a qr:

{{< image src="./images/qr.png" >}}

Trying to decode the QR directs us to a pastebin link:

```
https://pastebin.com/zhEhyp3G
```

Visiting the link above you will get a flag

---

##Flags

```the_flag_is{A_sP3c7r0grAm?!}```
