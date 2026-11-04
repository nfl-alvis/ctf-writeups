# Sweet_Tooth



{{< admonition tldr "TL;DR" >}}

**Sweet_Tooth** is a web challenge from **WMS**. Name: Sweet Tooth Value: 0 Description: Phantom Collective's CyberSpace forum looks like a normal security community.

{{< /admonition >}}

Name: Sweet Tooth
Value: 0
Description: Phantom Collective's CyberSpace forum looks like a typical security community.

=== TECHNICAL NOTES ===

The server provides a data collection endpoint for testing purposes:

  GET /collect/<token>?key=value — save data (free token, min 4 characters)
  GET /collect/<token>/view — view stored data

Example:
  fetch('/collect/mytoken123?data=hello')
  Then open: /collect/mytoken123/view


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/sweet-tooth/  

