---
title: "Time Capsule"
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

**Time Capsule** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description
This challenge provides a file named `time_capsule_repo.bundle` .  
From the name, the file indicates a Git repository wrapped in **Git bundle** format.

The objective of the challenge is to extract the repository, analyze the commit history, and find hidden flags.

---

## Solution Steps

### 1. Identify Bundle Files
The first step is to check the file type:

```bash
file time_capsule_repo.bundle
```
Output:

```
time_capsule_repo.bundle: Git bundle
```

This ensures that the file is a Git bundle that can be verified and cloned.

---

### 2. Bundle Verification

To ensure the bundle is valid and contains complete history, verify:

```bash
git bundle verify time_capsule_repo.bundle
```

The output indicates the bundle is valid and has complete history:

```
The bundle contains these 2 refs:
0fbca187d349b2f47850c548e649c7afdf9d8b8d refs/heads/master
0fbca187d349b2f47850c548e649c7afdf9d8b8d HEAD
The bundle records a complete history.
...
time_capsule_repo.bundle is okay
```

---

### 3. View Branch References

Next, check the available heads:

```bash
git bundle list-heads time_capsule_repo.bundle
```

Results:

```
0fbca187d349b2f47850c548e649c7afdf9d8b8d refs/heads/master
0fbca187d349b2f47850c548e649c7afdf9d8b8d HEAD
```

---

### 4. Clone Git Bundle

The bundle is then extracted into a Git repository using `git clone` :

```bash
git clone time_capsule_repo.bundle
```

The repository was successfully cloned to the `time_capsule_repo/` folder.

---

### 5. Payload Extraction

In the repository found the files `payload.zip` and `README.md` .

```bash
ls
```

Output:

```
payload.zip  README.md
```

ZIP extraction is performed:

```bash
unzip payload.zip
```

The result:

* `commit_info.txt` * `filler.bin` * `flag.txt` ---

### 6. Initial Analysis of Payload Contents

The contents of `commit_info.txt` show information on the number of commits:

```bash
cat commit_info.txt
```

Output:

```
commit_number: 100
generated_at: 2025-09-26T17:35:21Z
```

Meanwhile, `flag.txt` contains a string that looks like Base64, but apparently only contains a **fake flag**.

---

### 7. Enumerate all commits to find the original flag

Because the repository has many commits (100 commits), the next step is to iterate over all the commits.

Strategy used:

1. Checkout commits one by one
2. If `payload.zip` exists, extract it
3. If `flag.txt` exists, display its contents

Commands used:

```bash
for c in $(git rev-list --all --reverse); do
    git checkout $c --quiet 2>/dev/null
    if [ -f payload.zip ]; then
        unzip -o payload.zip >/dev/null 2>&1
        if [ -f flag.txt ]; then
            cat flag.txt
            echo
        fi
    fi
done
```

The output indicates almost all commits contain the pattern:

```
RkdURXtmYWtlX2ZsYWdfMDAxfQ==
RkdURXtmYWtlX2ZsYWdfMDAyfQ==
...
```

which if decoded is a fake flag.

However, in one of the commits a different string was found:

{{< image src="./images/solved.png" >}}

---

### 8. Decode Base64 to Get Flag

The string is then decoded using `base64` , and gets the flags:

Decoding result:

```
FGTE{REDACTED}
```

---

##Flags

```
FGTE{REDACTED}
```
