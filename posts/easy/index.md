# Easy



{{< admonition tldr "TL;DR" >}}

**easy** is a crypto challenge from **ctf.hackme.quest**. 526b78425233745561476c7a49476c7a4947566863336b7349484a705a3268305033303d

{{< /admonition >}}

## Challenge Description

526b78425233745561476c7a49476c7a4947566863336b7349484a705a3268305033303d

---

## Technical Analysis

Given a ciphertext that looks like the result of **hex** encoding. The first step is to decode the hex using `xxd` .

```bash
echo "526b78425233745561476c7a49476c7a4947566863336b7349484a705a3268305033303d" | xxd -r -p
```
Resulting output:

```txt
RkxBR3tUaGlzIGlzIGVhc3ksIHJpZ2h0P30=
```

The decode result still looks like the ciphertext encoded **Base64**. So the next step is to decode Base64.

```bash
echo "526b78425233745561476c7a49476c7a4947566863336b7349484a705a3268305033303d" | xxd -r -p | base64 -d
```

Output:

```txt
FLAG{This is easy, right?}
```

---

## Flags

```txt
FLAG{This is easy, right?}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/easy/  

