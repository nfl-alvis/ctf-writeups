---
title: "Where Can My Robot Go?"
date: 2026-02-22
categories:
  - Misc
tags:
  - Misc
  - CTFlearn
collections:
  - CTFlearn
---



{{< admonition tldr "TL;DR" >}}

**Where Can My Robot Go?** is a misc challenge from **CTFlearn**. Where do robots find what pages are on a website?

{{< /admonition >}}

## Description

Where do robots find what pages are on a website?

---

## Technical Analysis

The keyword "**robots**" in the description refers to the existence of the `robots.txt` file, which is a standard text file used to provide instructions to web crawlers (for example Googlebot) regarding directories or pages that may or may not be accessed (_crawled_).
Based on these assumptions, access is made to the following files:

```
https://ctflearn.com/robots.txt
```

{{< image src="./images/Robots.png" >}}

From the contents of the `robots.txt` file, the following configuration is obtained:

```
User-agent: *
Disallow: /70r3hnanldfspufdsoifnlds.html
```

This configuration indicates that all _user-agents_ are prohibited from accessing the path `/70r3hnanldfspufdsoifnlds.html` .

## Exploitation

Next, the path listed in the `Disallow` rule is accessed directly via the browser:

```
https://ctflearn.com/70r3hnanldfspufdsoifnlds.html
```

{{< image src="./images/Flag.png" >}}

From that page, the flag was successfully found.

---

##Flags

```CTFlearn{r0b0ts_4r3_th3_futur3}```
