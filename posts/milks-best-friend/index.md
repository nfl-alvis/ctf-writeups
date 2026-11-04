# Milk's Best Friend



{{< admonition tldr "TL;DR" >}}

**Milk's Best Friend** is a forensics challenge from **CTFlearn**. ---

{{< /admonition >}}

---

## Description 

There's nothing I love more than oreos, lions, and winning. 
[oreo.jpg](https://mega.nz/#!DC5F2KgR!P8UotyST_6n2iW5BS1yYnum8KnU0-2Amw2nq3UoMq0Y)
Have Fun :)

---

## Technical Analysis

Given an image `oreo.jpg` :

{{< image src="./images/oreo.jpg" >}}

The first step to take is to check for possible hidden files in the image using `binwalk` .

```bash
binwalk -e oreo.jpg
```

The command successfully extracts several files embedded in the image:

```
.
├── 252B.rar
├── a
└── b.jpg
```

These results indicate that `oreo.jpg` not only contains image data, but also contains several additional files.

---

## Flag Search

Next, a search for _string_ containing the word _flag_ is carried out in all extracted files using _command_ `strings` and `grep` .

```bash
strings * | grep flag
```

Output:

```
This is not the flag you are looking for.Q"t
This is not the flag you are looking for.
Finally, flag{eat_more_oreos}
```

The first two lines are _decoy_, while the last line contains a valid _flag_.

---

## Flags

```flag{eat_more_oreos}```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/milks-best-friend/  

