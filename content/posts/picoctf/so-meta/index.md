---
title: "picoCTF 2019 – Forensics: So Meta"
date: 2026-03-23
categories:
  - Forensics
tags:
  - Forensics
  - picoCTF
collections:
  - picoCTF
---



{{< admonition tldr "TL;DR" >}}

**picoCTF 2019 – Forensics: So Meta** is a forensics challenge from **picoCTF**. Find the flag in this [picture](https://challenge-files.picoctf.net/c_fickle_tempest/739119ebf098a2424ccce7d9e08e1af162dab0dc358950a6047750e37ec2bf2b/pico_img.png).

{{< /admonition >}}

## Description

Find the flag in this [picture](https://challenge-files.picoctf.net/c_fickle_tempest/739119ebf098a2424ccce7d9e08e1af162dab0dc358950a6047750e37ec2bf2b/pico_img.png).

<details>
  <summary><h2>Hint 1</summary>
  What does meta mean in the context of files?
</details>

<details>
  <summary><h2>Hint 2</summary>
  Ever heard of metadata?
</details>

---

## Objectives

The objective of this challenge is to extract the flag hidden within the **metadata** of an image file.

---

## Analysis

The hints indicate that the flag is not embedded in the visible image content, but rather in its **metadata**.

Metadata is information stored within a file that describes its properties, such as:

* Author
* Software used
* Creation details
* Hidden comments or custom fields

To inspect metadata in image files, we can use tools like `exiftool` .

---

## Approach

We analyze the image metadata using:

```bash
exiftool pico_img.png
```

From the output, we observe that the Artist field contains the flag.
To directly extract the flag, we can filter the output:

```bash
exiftool pico_img.png | grep -o "picoCTF{.*}"
```
