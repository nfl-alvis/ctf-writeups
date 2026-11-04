# So Many 64s




{{< admonition tldr "TL;DR" >}}

**So Many 64s** is a crypto challenge from **CTFlearn**. Help! My friend stole my flashdrive that had the flag on it. When he gave it back the flag was changed! Can you help me decrypt it?   [flag.txt](https://mega.nz/#!OHhUyIqA!H9WxSdG1O7eVcCm0dffggNB0-dBemSpBAXiZ0OXJnLk)

{{< /admonition >}}

##Challenge

Help! My friend stole my flashdrive that had the flag on it. When he gave it back the flag was changed! Can you help me decrypt it?  
[flag.txt](https://mega.nz/#!OHhUyIqA!H9WxSdG1O7eVcCm0dffggNB0-dBemSpBAXiZ0OXJnLk)

---

## Technical Analysis

### 1. Encoding Identification

The contents of the file `flag.txt` are a long string with typical **Base64** characters ( `A–Z` , `a–z` , `0–9` , `+` , `/` , `=` ).
Based on the challenge title *“So many 64s”*, it can be assumed that the string undergoes **multiple Base64 encoding**.

---

### 2. Decoding Process

To speed up the iterative decoding process, **CyberChef** is used with the following steps:

* Operation: **From Base64**
* Option: *Remove non-alphabet characters* is enabled
* The decoding process is carried out **repeatedly (loop)** until the output turns into readable plaintext

In CyberChef, the **Label + Jump** feature is used to perform automatic decoding repeatedly.

As a result, the flag was successfully obtained after doing **31 times Base64 decoding**.

{{< image src="./images/decoded.png" >}}

---

##Flags

```text
ABCTF{pr3tty_b4s1c_r1ght?}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/so-many-64s/  

