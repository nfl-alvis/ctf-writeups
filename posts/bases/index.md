# PicoCTF 2019 – General Skills: Bases


{{< admonition tldr "TL;DR" >}}
**picoCTF 2019 – General Skills: Bases** is a general skills challenge from **picoCTF**. What does this `bDNhcm5fdGgzX3IwcDM1` mean? I think it has something to do with bases.
{{< /admonition >}}

## Description

What does this `bDNhcm5fdGgzX3IwcDM1` mean? I think it has something to do with bases.

<details>
  <summary><h2>Hint</h2></summary>
  Submit your answer in our flag format. For example, if your answer was 'hello', you would submit 'picoCTF{hello}' as the flag.
</details>

---

## Objective

The objective of this challenge is to decode the given encoded string and format the result as a picoCTF flag.

---

## Analysis

The provided string:

```
bDNhcm5fdGgzX3IwcDM1
```

appears to be encoded rather than encrypted. Based on the hint referencing "bases", a strong candidate is **Base64 encoding**, which is commonly used in CTF challenges.

Characteristics of Base64:

* Uses characters: `A-Z`, `a-z`, `0-9`, `+`, `/`
* Often ends with `=` padding (though not always required)

The given string matches the Base64 character set and structure.

---

## Approach

To decode the string, we can use the `base64` utility available in most Unix-like systems:

```bash
echo "bDNhcm5fdGgzX3IwcDM1" | base64 -d
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/bases/  

