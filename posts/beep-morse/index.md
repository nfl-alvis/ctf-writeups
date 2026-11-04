# FGTE CTF: Beep Morse (Miscellaneous)




{{< admonition tldr "TL;DR" >}}

**FGTE CTF: Beep Morse (Miscellaneous)** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description
This challenge provides an audio file named `beep.wav` . Based on the description, the audio is **Morse code**, so the main task is to decode the Morse signal to obtain the flag.

---

## Solution Steps

### 1. Decode Morse Audio
The first step is to decode the contents of the `beep.wav` file using the web-based morse decoder in `morsefm.com` .

After the audio is processed, the decoding results are obtained in the form of text:

```

FOXTROT GOLF TANGO ECHO OPENBRACE PAPA UNIFORM LIMA THEN SIERRA ECHO CLOSEBRACE

```

---

### 2. Advanced Encoding Identification (NATO Phonetic Alphabet)
This result is not a direct flag, but uses the **NATO Phonetic Alphabet** format.

To decode it, take the **first letter** of each word:

- FOXTROT → F  
- GOLF → G  
- TANGO → T  
- ECHO → E  
- PAPA → P  
- UNIFORM → U  
- FIVE → L  
- SIERRA → S  
- ECHO → E  

Additionally:
- `OPENBRACE` represents `{` - `CLOSEBRACE` represents `}` - `THEN` is used as a separator between sections

---

### 3. Flag Reconstruction
By compiling the decoding results, we obtain:

```
FGTE{REDACTED}
```

---

##Flags

```
FGTE{REDACTED}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/beep-morse/  

