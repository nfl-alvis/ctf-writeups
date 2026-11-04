---
title: "Robots"
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

**Robots** is a web challenge from **NCKUCTF**. * **Challenge Name** : Robots * **Category** : WEB * **Organizer** : NKCUCTF * **Writeup Author** : spawm2pwn

{{< /admonition >}}

## **1. Identity Challenge**

* **Challenge Name** : Robots
* **Category** : WEB
* **Organizer** : NKCUCTF
* **Writeup Author** : spawm2pwn

---

## **2. Problem Description**

This challenge displays a simple web page with the message *“Nothing Here!”* without any direct indication of the presence of a FLAG. There are no input or interactive features that can be used directly by users.

The objective of this challenge is to find hidden locations that contain FLAGs.

---

## **3. System Analysis**

When accessing the main page:

```bash id="8g2p0m"
curl https://chall.nckuctf.org:28103/
```

The page just displays a message that there is nothing on it.

However, in web applications generally there are standard files such as:

* `robots.txt` This file is often used to manage search engine crawlers, but sometimes contains sensitive information.

---

## **4. Identify Vulnerabilities**

Performed access to files:

```bash id="0e5r1k"
curl https://chall.nckuctf.org:28103/robots.txt
```

Results:

```txt id="7j2nxa"
User-agent: *
Disallow: /hdw7vtg6cnbKJH/panel.php
```

Found hidden paths that are not permitted to crawlers, namely:

```
/hdw7vtg6cnbKJH/panel.php
```

The vulnerabilities that occur are:

> **Information Disclosure via robots.txt**

The `robots.txt` file leaks the location of a sensitive endpoint that should not be made public.

---

## **5. Exploitation Methodology**

### **5.1 Access robots.txt**

Retrieving hidden path information:

```bash id="g4m9zs"
curl https://chall.nckuctf.org:28103/robots.txt
```

---

### **5.2 Hidden Endpoint Access**

Accessing the found path:

```bash id="q2k8dn"
curl https://chall.nckuctf.org:28103/hdw7vtg6cnbKJH/panel.php
```

---

### **5.3 FLAG Extraction**

To retrieve the FLAG directly from the response:

```bash id="3r6h2y"
curl -s https://chall.nckuctf.org:28103/hdw7vtg6cnbKJH/panel.php | grep -o "NCKUCTF{.*}"
```

---

## **6. Exploitation Results**

Response page contains FLAG:

```html id="1m9qwe"
NCKUCTF{robots.txt_M4Y_3XP053_53Cr37_P47H}
```

FLAG obtained:

```id="9znc4t"
NCKUCTF{robots.txt_M4Y_3XP053_53Cr37_P47H}
```

---

## **7. Discussion**

The main problem in this challenge is:

* The `robots.txt` file is used to hide important paths
* However, the files can be accessed by anyone
* Sensitive information is actually exposed directly

It is necessary to understand that:

* `robots.txt` only serves as a **hint for crawlers**
* Not a security mechanism

So:

* All paths listed remain directly accessible to users

---

## **8. Improvement Recommendations**

To improve system security:

1. **Do not store sensitive information in robots.txt**
2. **Use authentication on critical endpoints**
3. **Restrict access using server-side controls**
4. **Use security methods such as:**

   * Authentication & Authorization
   * Access control (e.g. .htaccess / middleware)

---

## **9. Conclusion**

This challenge shows that:

> robots.txt is not a security mechanism

Using robots.txt to hide sensitive endpoints is an unsafe practice because the file is public and easily accessible.

---

## **10. Attachments**

### **Access Main Page**

```bash id="1v3mkl"
curl https://chall.nckuctf.org:28103/
```

### **Access robots.txt**

```bash id="2q7fvn"
curl https://chall.nckuctf.org:28103/robots.txt
```

### **Hidden Endpoint Access**

```bash id="8x2cpa"
curl https://chall.nckuctf.org:28103/hdw7vtg6cnbKJH/panel.php
```

### **FLAG Extraction**

```bash id="6r9kdz"
curl -s https://chall.nckuctf.org:28103/hdw7vtg6cnbKJH/panel.php | grep -o "NCKUCTF{.*}"
```
