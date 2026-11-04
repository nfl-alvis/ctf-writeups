---
title: "minfs"
date: 2026-05-09
categories:
  - Misc
tags:
  - Misc
  - BtS CTF
collections:
  - BtS CTF
---



{{< admonition tldr "TL;DR" >}}

**minfs** is a misc challenge from **BtS CTF**. In this challenge a remote instance is given which must be accessed using `snicat/sc` .

{{< /admonition >}}

## Challenge Description

In this challenge we are given a remote instance that must be accessed using `snicat/sc` .

Challenge information:

```text
tcp-minfs-1b51ff5e33283ee0.chall.bts.wh.edu.pl
```
Challenge provides hints:

```text
We are targeting a flag stored on a host with no utilities.
Can you manage to get it?
```

This indicates that the available environment is very minimal and most likely common Linux utilities such as `cat` , `ls` , `id` , and others are not available.

---

# Initial Enumeration

The connection is established using binary `sc` :

```bash
./sc tcp-minfs-1b51ff5e33283ee0.chall.bts.wh.edu.pl
```

When trying common commands like:

```bash
whoami
id
ls
```

no output is provided.

However, bash builtin commands such as `help` , `echo` , and `printf` still work.

Examples:

```bash
help
```

The output indicates that the shell used is still based on the bash builtin.

---

# Filesystem enumeration

Since `ls` is not available, filesystem enumeration is performed using bash's glob expansion:

```bash
echo /*
```

Output:

```text
/bin /dev /etc /flag /proc /sys
```

The `/flag` directory looked interesting so further enumeration was carried out:

```bash
echo /flag/*
```

Output:

```text
/flag/shard_1.png
/flag/shard_2.png
...
/flag/shard_12.png
```

There are 12 PNG files with the name `shard_X.png` .

---

# Utilizing socat

In this challenge Linux utility is very limited, but there is a binary `socat` .

Enumeration `/bin` :

```bash
echo *
```

Output:

```text
bash socat
```

This means that we can use `socat` as a replacement for utilities like `cat` .

---

# Create a Local Tunnel

To make it easier to interact with the challenge, bind mode from `sc` is used:

```bash
./sc -bind 3000 tcp-minfs-1b51ff5e33283ee0.chall.bts.wh.edu.pl
```

Then the connection is made locally:

```bash
nc localhost 3000
```

With this mechanism, all remote stdin/stdout are forwarded via the local socket.

---

# Retrieve PNG Files

Since `socat` is available, the file can be read directly to stdout:

```bash
printf 'socat FILE:/flag/shard_1.png STDOUT
' | nc localhost 3000 > shard_1.png
```

The entire shard is then retrieved using a loop:

```bash
for i in {1..12}; do
  printf "socat FILE:/flag/shard_${i}.png STDOUT
" | nc localhost 3000 > shard_${i}.png
done
```

File verification:

```bash
file shard_*.png
```

The output indicates all valid PNG files.

---

# Combining Shards

All images were then merged from left to right using Python and Pillow.

Scripts:

```python
from PIL import Image

files = [f"shard_{i}.png" for i in range(1, 13)]

images = [Image.open(f) for f in files]

total_width = sum(img.width for img in images)
max_height = max(img.height for img in images)

combined = Image.new("RGBA", (total_width, max_height))

x_offset = 0
for img in images:
    combined.paste(img, (x_offset, 0))
    x_offset += img.width

combined.save("flag_combined.png")
```

The final result is one complete image.

---

## Technical Analysis Image

The merged image contains many decimal numbers.

Examples:

```text
116 83 84 66 67 ...
```

The values are then converted to ASCII.

Example:

```text
116 -> t
83  -> S
84  -> T
66  -> B
```

After all the numbers are converted to ASCII sequentially, a challenge flag is obtained.

```
BtSCTF2026{00318f4bfrom_scratch_with_love3ff0e6b4}
```

---
