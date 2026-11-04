# Morse Code



{{< admonition tldr "TL;DR" >}}

**Morse Code** is a crypto challenge from **CTFlearn**. ..-. .-.. .- --. ... .- -- ..- . .-.. -- --- .-. ... . .. ... -.-. --- --- .-.. -... -.-- - .... . .-- .- -.-- .. .-.. .. -.- . -.-. .... . . ...

{{< /admonition >}}

## Description

..-. .-.. .- --. ... .- -- ..- . .-.. -- --- .-. ... . .. ... -.-. --- --- .-.. -... -.-- - .... . .-- .- -.-- .. .-.. .. -.- . -.-. .... . . ...

---

## Technical Analysis 

Given a _string_ with a pattern of dots ( `.` ) and lines ( `-` ) separated by spaces is a standard characteristic of **Morse Code**.

```
..-. .-.. .- --. ... .- -- ..- . .-.. -- --- .-. ... . .. ... -.-. --- --- .-.. -... -.-- - .... . .-- .- -.-- .. .-.. .. -.- . -.-. .... . . ...
```

---

## Decode Process

The decoding process is carried out by translating Morse Code into ASCII text with the following _command_:

```bash
echo "..-. .-.. .- --. ... .- -- ..- . .-.. -- --- .-. ... . .. ... -.-. --- --- .-.. -... -.-- - .... . .-- .- -.-- .. .-.. .. -.- . -.-. .... . . ..." | morse2ascii -
```

Output:

```
- decoded morse data:
flagsamuelmorseiscoolbythewayilikechees
```

---

## Flags

```flagsamuelmorseiscoolbythewayilikechees```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/morse-code/  

