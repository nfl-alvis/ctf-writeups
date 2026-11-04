# Taking LS




{{< admonition tldr "TL;DR" >}}

**Taking LS** is a forensics challenge from **CTFlearn**. Just take the Ls. Check out this zip file and I be the flag will remain hidden. [The Flag.zip](https://mega.nz/#!mCgBjZgB!_FtmAm8s_mpsHr7KWv8GYUzhbThNn0I8cHMBi4fJQp8)

{{< /admonition >}}

## Description

Just take the Ls. Check out this zip file and I be the flag will remain hidden.
[The Flag.zip](https://mega.nz/#!mCgBjZgB!_FtmAm8s_mpsHr7KWv8GYUzhbThNn0I8cHMBi4fJQp8)

---

## Technical Analysis

We are provided with the archive file `The Flag.zip` . Once extracted, the resulting file structure is as follows:

```bash
unzip 'The Flag.zip'
```

Output:

```
Archive:  The Flag.zip
   creating: The Flag/
  inflating: The Flag/.DS_Store
   creating: __MACOSX/
   creating: __MACOSX/The Flag/
  inflating: __MACOSX/The Flag/._.DS_Store
   creating: The Flag/.ThePassword/
  inflating: The Flag/.ThePassword/ThePassword.txt
  inflating: The Flag/The Flag.pdf
  inflating: __MACOSX/The Flag/._The Flag.pdf
```

From the extraction results, it can be seen that there is a hidden directory called `.ThePassword` .
Inside there is a file `ThePassword.txt` which is interesting to examine further.

The contents of the file are:

```
Nice Job!  The Password is "Im The Flag".
```

Next, Open the file `The Flag.pdf` and note that the file is protected by a password.

{{< image src="./images/Berpassword.png" >}}

---

## Completion

The password found in the `ThePassword.txt` file is:

```
Im The Flag
```

used for Opening the file `The Flag.pdf` .
Once the file is successfully opened, flags can be found within the document.

{{< image src="./images/Unlocked.png" >}}

---

##Flags

```ABCTF{T3Rm1n4l_is_C00l}```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/taking-ls/  

