# PicoCTF 2026 – Forensics: Timeline




{{< admonition tldr "TL;DR" >}}

**picoCTF 2026 – Forensics: Timeline** is a forensics challenge from **picoCTF**. This challenge provides a disk image named `partition4.img` . The objective is to locate a hidden flag within the filesystem and present it in the `___FL0X___` format.

{{< /admonition >}}

## Overview

This challenge provides a disk image named `partition4.img` . The objective is to locate a hidden flag within the filesystem and present it in the `___FL1X___` format.

The hints indicate that we should:

1. Create a **MAC timeline** using Sleuthkit tools.
2. Look for **anomalous timestamps**, which may result from **timestomping**.

Timestomping is a technique used to manipulate file timestamps to obscure malicious activity. Poorly executed timestomping often produces **impossible or very old timestamps**, which can be used to identify suspicious files.

---

## Initial Inspection

First, verify the filesystem type of the provided image:

```bash
file partition4.img
```

Output:

```
partition4.img: Linux rev 1.0 ext4 filesystem data
```

This confirms that the image contains an **ext4 filesystem**, which is fully supported by **The Sleuth Kit (TSK)**.

---

## Creating the Timeline

Following the hint, we generate a timeline of filesystem activity.

### Step 1: Generate Body File

```bash
fls -r -m / partition4.img > body.txt
```

* `fls` recursively lists files and directories.
* `-m /` assigns the root mount point.
* The output is saved in `body.txt` .

### Step 2: Generate the MAC Timeline

```bash
mactime -b body.txt > timeline.txt
```

This creates a chronological list of file events based on:

* **M** – Modified time
* **A** – Access time
* **C** – Metadata change time

---

## Identifying Suspicious Entries

The hint states:

> Sloppy timestomping can yield strange (very old) timestamps.

We therefore search for files with unusually old timestamps:

```bash
head timeline.txt
```

Among the earliest entries we find:

```
Wed Jan 02 1985 00:00:00       41 macb r/rrw-r--r-- 0 0 4945 /bin/bcab
```

This entry stands out because:

* The timestamp **1985** is highly unusual for a modern Linux filesystem.
* The filename **bcab** appears suspicious.
* It resides in `/bin` , which normally contains system binaries.

This strongly suggests that `/bin/bcab` is the target file.

---

## Locating the Inode

We need the inode number to extract the file from the disk image.

List the contents of `/bin` :

```bash
fls partition4.img 22
```

Relevant entries:

```
r/r 4945: bcab
```

This confirms:

* File name: `bcab` * Inode number: **4945**

---

## Extracting the File

We extract the file using `icat` :

```bash
icat partition4.img 4945 > bcab
```

Inspect the contents:

```bash
strings bcab
```

Output:

```
NzFtMzExbjNfMHU3MTEzcl9oM3JfNDNhMmU3YWYK
```

The string appears to be **Base64 encoded**.

---

## Decoding the Data

Decode the Base64 string:

```bash
cat bcab | base64 -d
```

Result:

```
71m311n3_0u7113r_h3r_43a2e7af
```

---

## Constructing the Flag

Wrap the decoded string with the picoCTF flag format:

```
picoCTF{71m311n3_0u7113r_h3r_43a2e7af}
```

---

## Final Flags

```
picoCTF{71m311n3_0u7113r_h3r_43a2e7af}
```

---

## Key Takeaways

1. **MAC timelines** are powerful for identifying anomalous file activity.
2. **Timestomping** attempts to hide malicious files by manipulating timestamps.
3. Poorly executed timestomping can result in **impossible or very old dates**, making detection easier.
4. Sleuthkit tools such as `fls` , `mactime` , and `icat` are essential for forensic analysis of disk images.

---

## Tools Used

* The Sleuth Kit ( `fls` , `mactime` , `icat` )
* `strings` * `base64` * Standard Linux utilities ( `grep` , `head` , `cat` )


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/timeline-0/  

