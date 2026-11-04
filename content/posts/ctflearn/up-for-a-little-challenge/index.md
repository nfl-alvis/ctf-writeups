---
title: "Up For A Little Challenge"
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

**Up For A Little Challenge** is a forensics challenge from **CTFlearn**. ---

{{< /admonition >}}

---

##Challenge

[Begin Hack.jpg](https://mega.nz/#!LoABFK5K!0sEKbsU3sBUG8zWxpBfD1bQx_JY_MuYEWQvLrFIqWZ0)  
You Know What To Do...

---

## Initial Analysis

The `Begin Hack.jpg` file opened normally and showed no suspicious visual indications.  
  
![Begin Hack.jpg](chall/Begin%20Hack.jpg)  
  
The first step is to perform **string analysis** to look for hidden information in the file structure.

```bash
strings "Begin Hack.jpg"
```

From the results, two important pieces of information were found:

```text
real_unlock_key: Nothing Is As It Seems
https://mega.nz/#!z8hACJbb!vQB569ptyQjNEoxIwHrUhwWu5WCj1JWmU-OFjf90Prg
```

* An **unlock key**
* A **Mega link** that leads to advanced files

---

## Advanced Archive Extraction

Mega link leads to archive file:

```
Up For A Little Challenge.zip
```

Once extracted, the directory structure obtained is:

```
.
├── Did I Forget Again?
│   ├── Loo Nothing Becomes Useless ack.jpg
│   └── .Processing.cerb4
└── __MACOSX (Metadata)
    ├── ._Did I Forget Again?
    └── Did I Forget Again?
        ├── ._Loo Nothing Becomes Useless ack.jpg
        └── ._.Processing.cerb4
```

The main focus is on hidden files:

```
.Processing.cerb4
```

---

## Technical Analysis of Hidden Files

File type checking is done using the command `file` :

```bash
file "Did I Forget Again?/.Processing.cerb4"
```

Output:

```
Zip archive data, made by v3.0 UNIX, extract using at least v2.0
```

The file turned out to be a **disguised ZIP archive** with an unusual extension.

---

## Archive Decryption

When extracted, the archive asks for a password.
Previously found unlock key used as password:

```
Nothing Is As It Seems
```

The extraction process was successful and generated a new file:

```
skycoder.jpg
```

---

## Discovery Flag

The `skycoder.jpg` file is opened visually.

{{< image src="./images/skycoder.jpg" >}}

By looking at the image in detail, you can see **red text in the bottom right corner of the image** which contains the challenge flag.

---

##Flags

```text
flag{hack_complete}
```
