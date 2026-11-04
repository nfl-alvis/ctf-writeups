# Redirect




{{< admonition tldr "TL;DR" >}}

**Redirect** is a web challenge from **NCKUCTF**. * **Challenge Name** : Redirect * **Category** : WEB * **Organizer** : NCKUCTF * **Writeup Author** : spawn2pwn

{{< /admonition >}}

## **1. Identity Challenge**

* **Challenge Name** : Redirect
* **Category** : WEB
* **Organizer** : NCKUCTF
* **Writeup Writer** : spawn2pwn

---

## **2. Problem Description**

This challenge is a simple web application that displays a *“Get Flag”* button. When the button is pressed, the user will be directed through several pages (*redirect*) before finally returning to the main page.

The objective of this challenge is to get the FLAG hidden behind the redirect mechanism.

---

## **3. System Analysis**

Based on initial analysis, the application flow is as follows:

1. User presses button:

```
   page1.php
   ```

2. The server performs a series of redirects:

   * `page1.php` → `flag.php` * `flag.php` → `page2.php` * `page2.php` → `/` (main page)

To analyze further, testing was carried out using the command:

```bash
curl -i -L https://chall.nckuctf.org:28101/page1.php
```

The results show that there are several HTTP `302 Found` responses which indicate a chain redirect.

---

## **4. Identify Vulnerabilities**

From the results of observations, it was found that:

* The `flag.php` file actually contains FLAGs
* However, normal access will always be directed (*redirect*) to another page before the FLAG is visible in the browser

The vulnerabilities that occur are:

> **Improper handling of HTTP redirect**

specifically:

* The server still sends the contents of the response (body) even though it gives the status `302 Found` * The browser automatically follows the redirect so that the content is not visible to the user

---

## **5. Exploitation Methodology**

### **5.1 Redirect Analysis**

Use the `-L` option on `curl` to follow the redirect:

```bash
curl -i -L https://chall.nckuctf.org:28101/page1.php
```

Visible redirect chain:

* `page1.php` → `flag.php` * `flag.php` → `page2.php` * `page2.php` → `/` However, the FLAG is not visible because of the automatic redirect.

---

### **5.2 Direct Access to Endpoints**

A direct request is made to the `flag.php` endpoint without following the redirect:

```bash
curl -i https://chall.nckuctf.org:28101/flag.php
```

---

## **6. Exploitation Results**

Response received:

```http
HTTP/1.1 302 Found
Location: page2.php

NCKUCTF{r3D1rC7_15_700_F457}
```

It can be seen that even though the server provides redirect status, **body response still contains FLAG**.

FLAG obtained:

```
NCKUCTF{r3D1rC7_15_700_F457}
```

---

## **7. Discussion**

The main problem in this challenge is:

* The server sends **sensitive data (FLAG)** in the response body
* Redirect only relies on the client (browser) mechanism
* No real access control on the `flag.php` endpoint 

Modern browsers:

* Automatically follow redirects
* Ignoring the body of the previous response

However, tools like `curl` allow:

* View raw responses
* Access data before the redirect occurs

---

## **8. Improvement Recommendations**

To prevent this vulnerability:

1. **Do not include sensitive data in the redirect response**
2. **Use server-side access control**

   * Validate whether the user has the right to access FLAG
3. **Separate redirect logic and data**

   * Do not send FLAG before validation is complete
4. **Use status codes correctly**

   * The redirect response should not contain sensitive information

---

## **9. Conclusion**

This challenge shows that:

> The redirect mechanism is not a security control

Data sent by the server can still be accessed if the user uses the right tools. Therefore, security must be done on the server side, rather than relying solely on browser behavior.

---

## **10. Attachments**

### **Request Follows Redirect**

```bash
curl -i -L https://chall.nckuctf.org:28101/page1.php
```

### **Request Without Following Redirect**

```bash
curl -i https://chall.nckuctf.org:28101/flag.php
```

### **Important Response**

```http
HTTP/1.1 302 Found
Location: page2.php

NCKUCTF{r3D1rC7_15_700_F457}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/redirect/  

