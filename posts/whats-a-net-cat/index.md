# PicoCTF 2019 – General Skills: What's a Net Cat?


{{< admonition tldr "TL;DR" >}}
**picoCTF 2019 – General Skills: what's a net cat?** is a general skills challenge from **picoCTF**. Using netcat (`nc`) is going to be pretty important. Can you connect to `fickle-tempest.picoctf.net` at port `51594` to get the flag?
{{< /admonition >}}

## Description

Using netcat (`nc`) is going to be pretty important. Can you connect to `fickle-tempest.picoctf.net` at port `51594` to get the flag?

<details>
  <summary><h2>Hint</h2></summary>
  nc tutorial
</details>

---

## Objective

The objective of this challenge is to connect to a remote service using Netcat and retrieve the flag.

---

## Analysis

This challenge introduces **Netcat (`nc`)**, a versatile networking tool used to:

* Establish TCP/UDP connections
* Interact with remote services
* Debug network services
* Transfer data

The challenge provides:

* Host: `fickle-tempest.picoctf.net`
* Port: `51594`

This indicates that a service is listening on that address and port, and we need to connect to it.

---

## Approach

We use Netcat to establish a TCP connection to the remote server:

```bash
nc fickle-tempest.picoctf.net 51594
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/whats-a-net-cat/  

