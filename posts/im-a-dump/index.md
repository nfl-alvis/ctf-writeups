# I'm a Dump




{{< admonition tldr "TL;DR" >}}

**I'm a dump** is a forensics challenge from **CTFlearn**. ---

{{< /admonition >}}

---

## Description

The keyword is hexadecimal, and removing an useless H.E.H.U.H.E. from the flag. The flag is in the format CTFlearn{*}

---

## Technical Analysis

Since the given file is an ELF file, the most appropriate approach is to perform a **hexadecimal dump** to look for strings containing the pattern `CTFlearn` .
According to the instructions, the characters `H` , `E` , and `U` are considered noise and need to be removed from the extraction result.

---

## Extraction Process

Commands used:

```bash
hexdump -C 883 | grep -A 2 "CTFlearn" | sed "s/[E.HU]//g"
```

1. `hexdump -C 883` Performs a _hexdump_ of the ELF file.
2. `grep -A 2 "CTFlearn"` Searches for lines containing the string `CTFlearn` .
3. `sed "s/[E.HU]//g"` Removes _noise_ characters as directed.

Output:

```
00001150  48 b8 43 54 46 6c 65 61  72 6e 48 ba 7b 66 6c 34  |CTFlearn{fl4|
00001160  67 67 79 66 48 89 45 d0  48 89 55 d8 48 c7 45 e0  |ggyf|
00001170  6c 34 67 7d 48 c7 45 e8  00 00 00 00 48 c7 45 f0  |l4g}|
```

If the ASCII parts of the dump are combined, they form a complete _flag_ string.

---

##Flags

```CTFlearn{fl4ggyfl4g}```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/im-a-dump/  

