# Pho Is Tasty!




{{< admonition tldr "TL;DR" >}}

**Pho Is Tasty!** is a forensics challenge from **CTFlearn**. ---

{{< /admonition >}}

---

##Challenge

The flag is hidden in the jpeg file. Good Luck! Have some Pho! Solve this challenge before solving my Scope challenge for 100 points.  
[Pho.jpg](https://ctflearn.com/challenge/download/971)

---

## Initial Analysis

The `Pho.jpg` image opens normally and shows no visual anomalies.

{{< image src="./images/Pho.jpg" >}}

The next step is to perform **byte level analysis** to look for possible hidden strings within the file structure.

---

## Binary Investigation

an inspection was performed using `hexdump` To view the hexadecimal and ASCII representation of the file:

```bash
hexdump -C Pho.jpg | head
```

Initial output shows valid JPEG headers ( `JFIF` ) as well as camera metadata.
However, at the beginning of the file **ASCII characters were found which formed a suspicious pattern**, even though they were interspersed with non-printable bytes ( `.` ).

Example of a dump:

```
|..C..T..F..l..e.|
|.a..r..n..{..I.|
|_..L..o..v..e._|
|..P..h..o..!..!|
|.!..}|
```

---

## Flag Extraction

Because the flag characters are separated by non-ASCII bytes, the `strings` command cannot successfully detect them.
To overcome this, the dot character ( `.` ) in the ASCII output is removed using `sed` , then a string search is performed `CTF` .

```bash
hexdump -C Pho.jpg | sed "s/[.]//g" | grep "CTF" -A 4
```

The result shows ASCII chunks that when combined form a complete flag:

```
CTFlearn{I_Love_Pho!!!}
```

---

##Flags

```text
CTFlearn{I_Love_Pho!!!}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/pho-is-tasty/  

