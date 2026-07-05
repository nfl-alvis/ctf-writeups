# Lo-Fi


{{< image src="./images/room_image.webp" >}}

> **Want to hear some lo-fi beats while relaxing or studying? We've got you covered!**

{{< admonition tldr "TL;DR" >}}
Lo-Fi is an easy TryHackMe room that demonstrates a Local File Inclusion (LFI) vulnerability. By exploiting the vulnerable `page` parameter, it is possible to read arbitrary files from the server, including `/etc/passwd`.
{{< /admonition >}}

## Overview

The target provides a web application with a simple interface.

{{< image src="./images/first-visit.webp" >}}

Clicking the **Sleep** option redirects the browser to the following URL:

```text
http://MACHINE_IP/?page=sleep.php
```

{{< image src="./images/sleep-page.webp" >}}

The application uses the `page` parameter to load `sleep.php`. Since user-controlled input is passed through a parameter that appears to select a file, this behavior suggests a potential Local File Inclusion (LFI) vulnerability.

An LFI vulnerability occurs when an application includes a file based on user input without properly validating or restricting the requested path. If successfully exploited, an attacker may be able to read sensitive files stored on the server.

A common file used to confirm LFI is `/etc/passwd`, as it contains information about local system accounts. I initially attempted to access it directly through the `page` parameter.

{{< image src="./images/bad-hacker.webp" >}}

The direct request was blocked, indicating that the application likely had a filter preventing paths that begin from the root directory.

{{< style "text-align:center" >}}
{{< image src="./images/monkey-confused.gif" width="60%" align="center" >}}
{{< /style >}}

To bypass this restriction, I referred to the File Inclusion section of the {{< link "https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion" PayloadsAllTheThings >}} repository. The repository contains useful payload references for a wide range of web vulnerabilities, including LFI bypass techniques.

Instead of referencing `/etc/passwd` directly from the root directory, I used directory traversal sequences to move upward through the filesystem before targeting the file. This technique is commonly known as **directory climbing**.

The payload successfully confirmed the LFI vulnerability.

{{< image src="./images/lfi-confirmed.webp" >}}

After confirming the vulnerability, I used the same directory traversal technique to search for the flag file. In TryHackMe rooms, flags are commonly stored in files such as `flag.txt`.

{{< image src="./images/flag-found.webp" >}}

## Conclusion

The Lo-Fi room demonstrates how insecure file inclusion can lead to Local File Inclusion vulnerabilities. The vulnerable `page` parameter allowed directory traversal, enabling access to files outside the intended application directory.

To prevent this issue, applications should avoid directly including files based on user-controlled input. A safer approach is to use an allowlist of permitted page names and map them to fixed server-side file paths.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://nfl-alvis.github.io/ctf-writeups/posts/lo-fi/  

