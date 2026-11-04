# Homepage



{{< admonition tldr "TL;DR" >}}

**homepage** is a web challenge from **ctf.hackme.quest**. [homepage](https://ctf.hackme.quest/) Where is the flag? Did you check the code?

{{< /admonition >}}

## Challenge Description

[homepage](https://ctf.hackme.quest/)  
Where is the flag? Did you check the code?

---

## Technical Analysis

Challenge provides a link that leads to the main CTF web page.

Upon opening the page, check the browser's Developer Tools. On the **Console** tab, a QR Code will be displayed.

{{< image src="./images/solve.png" >}}

The QR Code is then decoded to obtain the flag.

---

## Flags

```txt
FLAG{Oh, You found me!!!!!! Yeeeeeeee.}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/homepage/  

