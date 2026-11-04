---
title: "history"
date: 2026-02-22
categories:
  - Misc
tags:
  - Misc
  - FGTE
collections:
  - FGTE
---



{{< admonition tldr "TL;DR" >}}

**history** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description
This challenge provides an archive file named `secret.zip` . After successfully extracting, it was discovered that the contents of the archive contained a Git repository ( `.git` ) and an `flag.txt` which turned out to be a **decoy flag**. The original flag can be obtained by performing Git commit history analysis.

---

## Solution Steps

### 1. Extraction of `secret.zip` The first step is to try to extract the ZIP file.

```bash
unzip secret.zip
```
However, the output indicates that the archive is **password protected**, so the extraction process cannot continue without the correct password.

---

### 2. Bruteforce ZIP Password Using John

To get the password, the ZIP hash is extracted using `zip2john` , then cracked using the wordlist `rockyou.txt` .

```bash
zip2john secret.zip > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

The cracking results show that the password was successfully found:

```
iloveyou         (secret.zip)
```

---

### 3. Extract ZIP Using a Valid Password

Once the password is known, the archive is extracted again. The extraction results produce the `secret/` folder which contains the `.git` structure.

```bash
unzip secret.zip
```

In this folder, several important files were found:

* `secret/flag.txt` * `secret/.hint.txt` * `secret/.git/` ---

### 4. Identify the Decoy Flag

When opening the file `flag.txt` , it turns out that the flag that appears is not the original flag (decoy).
Since there is a Git repository, the next step is to check the commit history.

---

### 5. Analyze Git Commit History

Go to the `secret/` folder, then look at the commit history using `git log` .

```bash
cd secret
git log
```

The output indicates there are 2 commits:

```
commit 745debc7dc41c49f1f7df024fd77e97132554e47 (HEAD -> main)
    update

commit d6158ab833a564ec3aca8552b295dd6958ff8f2c
    init
```

The latest commit ( `update` ) allegedly made changes that removed the original flag.

---

### 6. Retrieve Flags from Old Commits

To view changes to the initial commit, use the command:

```bash
git show d6158ab833a564ec3aca8552b295dd6958ff8f2c
```

In the diff output it can be seen that file `flag.txt` in commit `init` contains the original flags:

```diff
+FGTE{Redacted}
```

---

##Flags

```
FGTE{Redacted}
```
