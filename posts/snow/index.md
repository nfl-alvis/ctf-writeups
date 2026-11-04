# Snow




{{< admonition tldr "TL;DR" >}}

**snow** is a forensics challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

In this challenge, two files are given:

* `note.txt` * `snow.jpg` The contents of `note.txt` at first glance look normal, but there are irregularities in the form of many empty lines and the possibility of using hidden spaces:

```
Salju menutupi banyak hal, tapi bukan semuanya hilang.
Perhatikan foto musim dingin — mungkin ada sesuatu yang tersembunyi di balik lapisan putih.
pass: snowflake
```

Additionally, We are provided with the image file `snow.jpg` which visually shows no indication of manipulation or suspicious artifacts.

{{< image src="./images/snow.jpg" >}}

---

## Solution Steps

### 1. Initial Analysis

Instructions in text descriptions such as:

> “Snow covers a lot of things...”
> “...something hidden beneath the white layer.”

indicates the possibility of using **steganography** techniques.

The presence of many spaces and blank lines in `note.txt` leads to suspicions of using the *whitespace steganography* method, especially the **StegSnow** tool which hides data in space and tab characters at the end of the line.

Apart from that, there is the word `pass: snowflake` which is thought to be a passphrase for the extraction process.

---

### 2. Data Extraction from `note.txt` The extraction process is carried out using `stegsnow` with the available passphrase:

```bash
stegsnow -p snowflake -C note.txt
```

Output obtained:

```
frozzen_99_❄️⛄
```

The extracted string looks like an advanced passphrase. This strengthens the suspicion that this challenge uses layered steganography (*multi-layer steganography*).

---

### 3. Data Extraction from `snow.jpg` The passphrase resulting from the previous stage is then used to extract data from the image file using `steghide` :

```bash
steghide extract -sf snow.jpg -p "frozzen_99_❄️⛄"
```

Output:

```
wrote extracted data to "flag.txt".
```

The file `flag.txt` was successfully extracted.

---

### 4. Content Analysis `flag.txt` The contents of `flag.txt` are a series of snow and snowman emojis:

```
❄⛄❄❄❄⛄⛄❄❄⛄❄❄⛄⛄❄❄❄⛄❄❄❄❄❄⛄❄⛄❄❄❄⛄⛄⛄❄⛄⛄⛄⛄❄⛄⛄❄⛄⛄⛄❄⛄⛄⛄❄⛄⛄❄⛄❄❄⛄❄⛄⛄❄⛄⛄⛄❄❄⛄⛄⛄❄⛄❄❄❄⛄⛄❄❄⛄❄⛄❄⛄⛄⛄❄❄⛄❄❄⛄❄⛄⛄⛄⛄⛄❄⛄⛄❄⛄❄❄⛄❄⛄⛄⛄❄❄⛄⛄❄⛄❄⛄⛄⛄⛄⛄❄⛄⛄❄⛄❄❄❄❄⛄⛄❄❄⛄❄⛄❄⛄⛄⛄❄❄⛄❄❄⛄⛄❄❄⛄❄⛄❄⛄❄⛄⛄⛄⛄⛄❄❄⛄⛄❄❄⛄❄❄❄⛄⛄❄❄❄❄❄❄⛄⛄❄❄⛄❄❄❄⛄⛄❄⛄❄⛄❄⛄⛄⛄⛄⛄❄⛄
```

At this stage, it can be seen that only two types of characters are used:

* ❄ (snowflakes)
* ⛄ (snowman)

Since there are only two different symbols, it is likely that this is a binary representation.

---

### 5. Decoding Process

Mapping is carried out as follows:

```
❄️ = 0
⛄ = 1
```

After all symbols are converted to binary form, the results are grouped by 8 bits and converted to ASCII representation.

The decoding process produces a string which is a flag in FLAG{ format.

After adjusting to the flag format used in the competition (FGTE), a valid final flag was obtained.

{{< image src="./images/solved.png" >}}

---

## Flags

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/snow/  

