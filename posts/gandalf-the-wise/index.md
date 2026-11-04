# GandalfTheWise




{{< image src="./images/gandalf.webp" >}}

> **Extract the flag from the Gandalf.jpg file. You may need to write a quick script to solve this.**

{{< admonition tldr "TL;DR" >}}

**GandalfTheWise** is an intermediate steganography challenge that embeds multiple JPEG comment markers ( `0xFFFE` ) containing Base64-encoded strings. Decoding the first comment reveals a decoy flag and hint: `___FL0X___` . Decoding the second and third comments into raw byte buffers and computing their byte-by-byte XOR reveals the true flag.

{{< /admonition >}}

## 1. Visual & Metadata Inspection

The provided image `Gandalf.jpg` displays an image of Gandalf. Opening the file in standard photo viewers reveals no obvious visual artifacts or watermarks.

{{< image src="./images/gandalf.webp" >}}

---

## 2. File Structure & Header Analysis

We examine the file's raw byte stream using `xxd` :

```bash
xxd Gandalf.jpg | head -n 12
```

```text
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

In the JPEG specification, the marker sequence `FF FE` defines a **Comment Section (COM)** containing arbitrary metadata. We observe three distinct comment sections containing Base64-encoded text:

```text
1: Q1RGbGVhcm57eG9yX2lzX3lvdXJfZnJpZW5kfQo=
2: xD6kfO2UrE5SnLQ6WgESK4kvD/Y/rDJPXNU45k/p
3: h2riEIj13iAp29VUPmB+TadtZppdw3AuO7JRiDyU
```

---

## 3. Decoding the First Comment (The Hint)

Decoding comment 1:

```bash
echo "Q1RGbGVhcm57eG9yX2lzX3lvdXJfZnJpZW5kfQo=" | base64 -d
```

Output:

```text
CTFlearn{xor_is_your_friend}
```

Submitting this value on CTFlearn fails. The text serves as an explicit clue informing us that the remaining two comments must be combined using the **XOR** operation.

---

## 4. Byte-wise XORing Comments 2 and 3

We decode comments 2 and 3 to their raw hexadecimal representations:

```bash
echo "xD6kfO2UrE5SnLQ6WgESK4kvD/Y/rDJPXNU45k/p" | base64 -d | xxd -p
```

```text
c43ea47ced94ac4e529cb43a5a01122b892f0ff63fac324f5cd538e64fe9
```

```bash
echo "h2riEIj13iAp29VUPmB+TadtZppdw3AuO7JRiDyU" | base64 -d | xxd -p
```

```text
876ae21088f5de2029dbd5543e607e4da76d669a5dc3702e3bb251883c94
```

We compute the bitwise XOR between these two equal-length hex strings:

{{< image src="./images/xor.webp" >}}

Using Python to perform the XOR:

```python
s1 = bytes.fromhex(
    "c43ea47ced94ac4e529cb43a5a01122b892f0ff63fac324f5cd538e64fe9"
)
s2 = bytes.fromhex(
    "876ae21088f5de2029dbd5543e607e4da76d669a5dc3702e3bb251883c94"
)

result = bytes([a ^ b for a, b in zip(s1, s2)])
print(result.decode())
```

Output:

```text
CTFlearn{Gandalf.BilboBaggins}
```

---

##Flags

```text
CTFlearn{Gandalf.BilboBaggins}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/gandalf-the-wise/  

