# Tone Dialing




{{< admonition tldr "TL;DR" >}}

**Tone Dialing** is a crypto challenge from **CTFlearn**. At 1pm I called my uncle who was 64 years old 10 months ago, but I heard only that. Later I started thinking about the 24 hour clock.   I hope you will help me solve this problem.   [you_know_what_to_do.wav](https://ctflearn.com/challenge/download/889)

{{< /admonition >}}

##Challenge

At 1pm I called my uncle who was 64 years old 10 months ago, but I heard only that. Later I started thinking about the 24 hour clock.  
I hope you will help me solve this problem.  
[you_know_what_to_do.wav](https://ctflearn.com/challenge/download/889)

---

## Technical Analysis

We are provided with an audio file named `you_know_what_to_do.wav` . When played, the audio does not contain the sound of human conversation, but rather short tones that resemble the sound of classic telephone buttons.  

These sound characteristics are identical to **DTMF (Dual-Tone Multi-Frequency)**, which is a tone system used on telephone keypads to represent numbers (0–9) and certain symbols.

Based on the challenge description and the type of audio provided, it can be assumed that important information (flags) are hidden in the form of **DTMF tone sequence**.

---

## Decoding Process

To extract digits from DTMF tones, the audio is analyzed using an online decoder:

[**DTMF Decoder**](https://dtmf.netlify.app/)

After the audio file is uploaded and processed, the decoding result is obtained in the form of a series of numbers:

```
67847010810197110123678289808479718265807289125
```

---

## Interpreting the Results

The series of numbers is then assumed to represent combined **ASCII decimal codes**.

By separating the numbers into valid ASCII values and converting them to characters, the following string is obtained:

```
CTFlearn{CRYPTOGRAPHY}
```

---

##Flags

```text
CTFlearn{CRYPTOGRAPHY}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/tone-dialing/  

