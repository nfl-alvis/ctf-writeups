---
title: "Corridor"
date: 2026-07-11
categories:
  - Web
tags:
  - IDOR
collections:
  - TryHackMe
---

{{< image src="./images/room_image.webp" >}}

> **Can you escape the Corridor?**

{{< admonition tldr "TL;DR" >}}
**Corridor** is an easy TryHackMe room that introduces the concept of **Insecure Direct Object References (IDOR)**. By analyzing the application's URL structure, we discover that each endpoint is simply the MD5 hash of a sequential integer. Since the application only exposes hashes for the numbers **1–13**, generating the MD5 hash for **0** allows us to access a hidden endpoint and retrieve the flag.
{{< /admonition >}}

## Scenario

In this room, we are placed inside a mysterious corridor filled with multiple doors. The challenge description hints at an **IDOR vulnerability** and encourages us to pay close attention to the URL endpoints while navigating the application.

> _You have found yourself in a strange corridor. Can you find your way back to where you came? Examine the URL endpoints you access as you navigate the website and note the hexadecimal values you find. They look an awful lot like a hash, don't they?_

Upon visiting the application, we are presented with the following page.

{{< image src="./images/first-visit.webp" >}}

Each door is clickable, so I opened one of them to see where it would lead.

{{< image src="./images/door-redirect.webp" >}}

The page simply displays an empty white room with no visible content. Inspecting the page source also reveals nothing particularly interesting.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    ...
</head>

<body>

<style>
    body{
        background-image: url("/static/img/empty_room.jpg");
        background-size: cover;
    }
</style>

</body>
</html>
```

Since the destination page did not reveal anything useful, I returned to the main corridor and inspected its source code instead.

```html
<map name="image-map">
    <area alt="c4ca4238a0b923820dcc509a6f75849b" href="c4ca4238a0b923820dcc509a6f75849b">
    <area alt="c81e728d9d4c2f636f067f89cc14862c" href="c81e728d9d4c2f636f067f89cc14862c">
    <area alt="eccbc87e4b5ce2fe28308fd9f2a7baf3" href="eccbc87e4b5ce2fe28308fd9f2a7baf3">
    ...
</map>
```

Immediately, something stood out. Every door pointed to an endpoint consisting of a **32-character hexadecimal string**. Combined with the challenge description mentioning hexadecimal values, it strongly suggested that these values were **MD5 hashes** rather than random identifiers.

To verify this assumption, I used **CrackStation** to identify several of the hashes. The results confirmed that they were simply MD5 hashes of sequential integers.

| MD5 Hash                           | Value |
|------------------------------------|-------|
| `c4ca4238a0b923820dcc509a6f75849b` | `1`   |
| `c81e728d9d4c2f636f067f89cc14862c` | `2`   |
| `eccbc87e4b5ce2fe28308fd9f98764da` | `3`   |
| ...                                | ...   |

At this point, it became clear that the application only exposed doors corresponding to the numbers **1 through 13**.

Since the room description hinted at finding a location we were **not intended to access**, the next logical step was to generate the MD5 hash for **0**, which was missing from the corridor.

This can be done locally with:

```bash
echo -n "0" | md5sum
```

Output:

```text
cfcd208495d565ef66e7dff9f98764da
```

Finally, I visited the following endpoint:

```text
http://MACHINE_IP/cfcd208495d565ef66e7dff9f98764da
```

Instead of another empty room, the application returned the flag.

{{< image src="./images/get-flag.webp" >}}

## Explanation

This challenge demonstrates a classic **Insecure Direct Object Reference (IDOR)**.

The application attempts to hide resources by replacing simple numeric identifiers with their MD5 hashes. However, **hashing is not authorization**. Since the identifiers are predictable (`0`, `1`, `2`, `3`, ...), an attacker can easily generate the corresponding hashes and access resources that were never linked in the user interface.

In this case, the developer assumed that hiding object identifiers behind MD5 hashes would prevent users from discovering other endpoints. Because the identifiers followed a predictable sequence, generating the hash for the missing value (`0`) allowed us to access the hidden resource and obtain the flag.
