---
title: "GandalfTheWise"
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

**GandalfTheWise** is a forensics challenge from **CTFlearn**. Extract the flag from the Gandalf.jpg file. You may need to write a quick script to solve this.   [Gandalf.jpg](https://ctflearn.com/challenge/download/936)

{{< /admonition >}}

##Challenge

Extract the flag from the Gandalf.jpg file. You may need to write a quick script to solve this.  
[Gandalf.jpg](https://ctflearn.com/challenge/download/936)

---

## Technical Analysis

### 1. Visual Analysis

The `Gandalf.jpg` file is opened normally using the image viewer.
No visual indications such as hidden text, watermarks, or suspicious distortion were found.

{{< image src="./images/Gandalf.jpg" >}}

Since there are no visual clues, the analysis proceeds to the **byte** level.

---

### 2. File Structure Analysis (Hex)

Perform initial inspection using `xxd` To view the contents of the byte file:

```bash
xxd Gandalf.jpg | head -10
```

Output:

```
00000000: ffd8 ffe0 0010 4a46 4946 0001 0100 0001  ......JFIF......
00000010: 0001 0000 fffe 002b 5131 5247 6247 5668  .......+Q1RGbGVh
00000020: 636d 3537 6547 3979 5832 6c7a 5833 6c76  cm57eG9yX2lzX3lv
00000030: 6458 4a66 5a6e 4a70 5a57 356b 6651 6f3d  dXJfZnJpZW5kfQo=
00000040: 0aff fe00 2b78 4436 6b66 4f32 5572 4535  ....+xD6kfO2UrE5
00000050: 536e 4c51 3657 6745 534b 346b 7644 2f59  SnLQ6WgESK4kvD/Y
00000060: 2f72 444a 5058 4e55 3435 6b2f 700a fffe  /rDJPXNU45k/p...
00000070: 002b 6832 7269 4549 6a31 3369 4170 3239  .+h2riEIj13iAp29
00000080: 5655 506d 422b 5461 6474 5a70 7064 7733  VUPmB+TadtZppdw3
```

It can be seen that there are several **JPEG Comment Markers ( `0xFFFE` )** which contain Base64 strings.

Found three comments:

```
Q1RGbGVhcm57eG9yX2lzX3lvdXJfZnJpZW5kfQo=
xD6kfO2UrE5SnLQ6WgESK4kvD/Y/rDJPXNU45k/p
h2riEIj13iAp29VUPmB+TadtZppdw3AuO7JRiDyU
```

---

### 3. Decode the First Comment

The first comment is decoded using Base64:

```bash
echo "Q1RGbGVhcm57eG9yX2lzX3lvdXJfZnJpZW5kfQo=" | base64 -d
```

Output:

```
CTFlearn{xor_is_your_friend}
```

However, this flag **is invalid** when submitted.
The message is more properly considered a **hint**, not a final flag, which leads to the use of **XOR**.

---

### 4. XOR the Remaining Two Comments

The other two Base64 comments are then decoded and converted to hexadecimal representation.

Second comment:

```bash
echo "xD6kfO2UrE5SnLQ6WgESK4kvD/Y/rDJPXNU45k/p" | base64 -d | xxd -p
```

Output:

```
c43ea47ced94ac4e529cb43a5a01122b892f0ff63fac324f5cd538e64fe9
```

Third comment:

```bash
echo "h2riEIj13iAp29VUPmB+TadtZppdw3AuO7JRiDyU" | base64 -d | xxd -p
```

Output:

```
876ae21088f5de2029dbd5543e607e4da76d669a5dc3702e3bb251883c94
```

The two hexadecimal values are then XORed byte-by-byte using tools such as [**xor.pw**](https://xor.pw/).

{{< image src="./images/xor.png" >}}

XOR Results:

```
4354466c6561726e7b47616e64616c662e42696c626f42616767696e737d
```

---

### 5. Convert XOR Results to ASCII

The resulting hexadecimal value of the XOR is converted back to ASCII:

```bash
echo "4354466c6561726e7b47616e64616c662e42696c626f42616767696e737d" | xxd -r -p
```

Output:

```
CTFlearn{Gandalf.BilboBaggins}
```

---

##Flags

```text
CTFlearn{Gandalf.BilboBaggins}
```
