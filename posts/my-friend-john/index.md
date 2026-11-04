# My Friend John




{{< admonition tldr "TL;DR" >}}

**My Friend John** is a misc challenge from **CTFlearn**. Have you met my friend John?   He's not so scary, even though they call him "The Ripper".   [MyFriendJohn.zip](https://ctflearn.com/challenge/download/1135)

{{< /admonition >}}

##Challenge

Have you met my friend John?  
He's not so scary, even though they call him "The Ripper".  
[MyFriendJohn.zip](https://ctflearn.com/challenge/download/1135)

---

## Technical Analysis

This challenge indicates the use of **John The Ripper** as the main method, reinforced by the nickname *"The Ripper"* as well as the file name `use-rockyou.zip` .

The first step is to extract the main archive:

```bash
unzip MyFriendJohn.zip
```

Output:

```
Archive:  MyFriendJohn.zip
 extracting: use-rockyou.zip
```

The `use-rockyou.zip` file turns out to be **password protected**, so a cracking process is required.

---

## Stage 1 — Cracking `use-rockyou.zip` Generate zip hash:

```bash
zip2john use-rockyou.zip > use-rockyou.hash
```

Crack using John:

```bash
john use-rockyou.hash
```

Output:

```
kdbs0429 (use-rockyou.zip)
```

Archive extraction:

```bash
unzip use-rockyou.zip
```

Output:

```
inflating: custom-list.txt
extracting: custom-list.zip
```

Found:

* `custom-list.txt` → Custom wordlist
* `custom-list.zip` → Passworded archive

---

## Stage 2 — Cracking `custom-list.zip` Generate hash:

```bash
zip2john custom-list.zip > custom-list.hash
```

Crack using the wordlist provided:

```bash
john --wordlist=custom-list.txt custom-list.hash
```

Output:

```
1N73rD3N0M1N4710N41 (custom-list.zip/brute-force-pin.zip)
```

Archive extraction:

```bash
unzip custom-list.zip
```

Output:

```
extracting: brute-force-pin.zip
```

---

## Stage 3 — Cracking `brute-force-pin.zip` Generate hash:

```bash
zip2john brute-force-pin.zip > brute-force-pin.hash
```

Crack with rockyou wordlist:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt brute-force-pin.hash
```

Output:

```
991337 (brute-force-pin.zip/flag.txt)
```

Final archive extraction:

```bash
unzip brute-force-pin.zip
```

Output:

```
extracting: flag.txt
```

---

##Flags

```
CTFlearn{s0_n0W_y0uv3_M3t_J0hN}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/my-friend-john/  

