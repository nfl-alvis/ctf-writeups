# Digital Camouflage




{{< admonition tldr "TL;DR" >}}

**Digital Camouflage** is a forensics challenge from **CTFlearn**. This writeup provides a complete walkthrough and technical analysis.

{{< /admonition >}}

## Challenge Overview

We need to gain access to some routers. Let's try and see if we can find the password in the captured network data: [data.pcap](https://mega.nz/#!XDBDRAQD!4jRcJvAhMkaVaZCOT3z3zkyHre2KHfmkbCN5lYpiEoY)  
Hint 1: It looks like someone logged in with their password earlier. Where would log in data be located in a network capture?  
Hint 2: If you think you found the flag, but it doesn't work, consider that the data may be encrypted.  
  
Credit: picoCTF 2017

---

## Technical Analysis & Exploitation

### 1. Open the Capture file

The `data.pcap` file is opened using **Wireshark** to perform network traffic analysis.
Because the web login process generally uses the HTTP protocol, filtering is carried out on HTTP traffic.

In addition, the **Follow → HTTP Stream** feature is used to view the complete conversation between the client and server.

---

### 2. Find Login Data

On the **3rd HTTP Stream**, a `POST` request was found which leads to the login page:

{{< image src="./images/login.png" >}}

```http
POST /pages/main.html HTTP/1.1
```

In the body of the request, there are login parameters in the form of `userid` and `password` :

```
userid=hardawayn&pswrd=UEFwZHNqUlRhZQ%3D%3D
```

This shows that:

* **Username:** `hardawayn` * **Password:** is still in encoded form

---

### 3. Decode URLs

The password value contains the characters `%3D` , which indicates **URL encoding**.
Perform URL decoding first:

```
UEFwZHNqUlRhZQ%3D%3D
```

Become:

```
UEFwZHNqUlRhZQ==
```

---

### 4. Base64 Decode

The resulting URL decode string is clearly **Base64 encoding**.
Base64 decoding is carried out to obtain the original plaintext.

Decoding result:

```
PApdsjRTae
```

---

##Flags

```text
PApdsjRTae
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/digital-camouflage/  

