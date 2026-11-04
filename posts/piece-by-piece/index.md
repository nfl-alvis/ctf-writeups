# PicoCTF 2026 – General Skills: Ping Command




{{< admonition tldr "TL;DR" >}}

**picoCTF 2026 – General Skills: Ping Command** is a misc challenge from **picoCTF**. This challenge provides SSH access to a remote Linux system. The objective is to analyze the files available on the server and reconstruct a split archive that contains the flag.

{{< /admonition >}}

## Overview

This challenge provides SSH access to a remote Linux system. The objective is to analyze the files available on the server and reconstruct a split archive that contains the flag.

The challenge demonstrates basic Linux file manipulation techniques such as file concatenation and archive extraction.

---

## Initial Access

The challenge provides SSH credentials to access the remote machine.

```bash
ssh -p 64080 ctf-player@dolphin-cove.picoctf.net
```

After entering the provided password, the user gains access to a minimal Ubuntu environment.

---

## Enumerating the Files

Once connected, list the files in the current directory:

```bash
ls
```

Output:

```
instructions.txt
part_aa
part_ab
part_ac
part_ad
part_ae
```

Listing with additional details:

```bash
ls -la
```

This confirms that several files named `part_*` are present along with an `instructions.txt` file.

---

## Reading the Instructions

The instructions file contains hints about how to recover the flag.

```bash
cat instructions.txt
```

Output:

```
Hint:

- The flag is split into multiple parts as a zipped file.
- Use Linux commands to combine the parts into one file.
- The zip file is password protected. Use this "supersecret" password to extract the zip file.
- After unzipping, check the extracted text file for the flag.
```

From these instructions we learn:

1. The zip archive has been split into several parts.
2. The parts must be combined into a single archive.
3. The archive is password-protected.
4. The password is `supersecret` .

---

## Identifying the File Type

To verify the file format of the split files:

```bash
file part_*
```

Output:

```
part_aa: Zip archive data
part_ab: data
part_ac: data
part_ad: data
part_ae: data
```

This indicates that the files represent segments of a single ZIP archive.

---

## Reconstructing the ZIP Archive

The split files can be recombined using the `cat` command:

```bash
cat part_* > final.zip
```

This concatenates all parts into a single archive named `final.zip` .

---

## Extracting the Archive

Next, extract the archive using `unzip` :

```bash
unzip final.zip
```

The archive requests a password.

```
password: supersecret
```

After entering the password, the archive extracts a file named `flag.txt` .

---

## Retrieving the Flag

View the contents of the extracted file:

```bash
cat flag.txt
```

Output:

```
picoCTF{z1p_and_spl1t_f1l3s_4r3_fun_78b76e61}
```

---

## Final Flags

```
picoCTF{z1p_and_spl1t_f1l3s_4r3_fun_78b76e61}
```

---

## Key Takeaways

* Large files can be split into multiple segments and later recombined.
* The `cat` command can be used to concatenate file parts.
* Password-protected archives require the correct password during extraction.
* Basic Linux commands are often sufficient to solve many CTF challenges.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/piece-by-piece/  

