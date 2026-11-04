---
title: "Scoreboard"
date: 2026-02-11
categories:
  - Web
tags:
  - HTTP Headers
  - Information Disclosure
  - curl
collections:
  - ctf.hackme.quest
---

> **Check out the scoreboard! DO NOT ATTACK or scan the scoreboard; you don't need to do that.**

{{< admonition tldr "TL;DR" >}}
**Scoreboard** is a web reconnaissance challenge emphasizing the importance of examining raw HTTP response headers. The challenge instructions strictly prohibit aggressive fuzzing or vulnerability scanning. Requesting the server headers via `curl -I` reveals a custom HTTP header (`x-flag`) exposing the flag directly.
{{< /admonition >}}

## Analysis

The challenge description links to the CTF platform's scoreboard endpoint:

```text
https://ctf.hackme.quest/scoreboard
```

The author explicitly warns against port scanning, fuzzing, or attacking the application. This implies that the flag is not secured behind an exploit chain or authentication bypass, but is exposed through standard protocol metadata.

---

## Inspecting HTTP Response Headers

When troubleshooting or conducting passive reconnaissance on web applications, inspecting the HTTP headers sent by the server often discloses hidden server software versions, debug configurations, or embedded flags.

We send a `HEAD` request using `curl -I`:

```bash
curl -I "https://ctf.hackme.quest/scoreboard/?capture=the_flag"
```

```http
HTTP/2 200
server: nginx/1.24.0 (Ubuntu)
date: Wed, 11 Feb 2026 14:03:57 GMT
content-type: text/html; charset=UTF-8
x-flag: FLAG{Header can hide some data aswell.}
set-cookie: session_guard=623phcriikmfja7ag6o7k9l1mv; expires=Wed, 18-Feb-2026 14:03:57 GMT; Max-Age=604800; path=/; domain=ctf.hackme.quest; secure; HttpOnly
expires: Thu, 19 Nov 1981 08:52:00 GMT
cache-control: no-store, no-cache, must-revalidate
pragma: no-cache
```

The server response includes an anomalous custom response header:

```http
x-flag: FLAG{Header can hide some data aswell.}
```

---

## Flag

```text
FLAG{Header can hide some data aswell.}
```

---

## Security Takeaway

Custom and debug HTTP headers are often added during development for telemetry, routing, or staging verification. If left unmanaged, they can inadvertently disclose sensitive data, internal routing IDs, or software versions to untrusted clients. Always ensure production servers strip unneeded custom headers via web server reverse proxy rules.
