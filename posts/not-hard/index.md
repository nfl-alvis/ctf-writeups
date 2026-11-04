# Not Hard



{{< admonition tldr "TL;DR" >}}

**not hard** is a crypto challenge from **ctf.hackme.quest**. `Nm@rmLsBy{Nm5u-K{iZKPgPMzS2I*lPc%_SMOjQ#O;uV{MM*?PPFhk|Hd;hVPFhq{HaAH<` Tips: `pydoc3 base64`

{{< /admonition >}}

## Challenge Description `Nm@rmLsBy{Nm5u-K{iZKPgPMzS2I*lPc%_SMOjQ#O;uV{MM*?PPFhk|Hd;hVPFhq{HaAH<` Tips: `pydoc3 base64` ---

## Technical Analysis

Given a ciphertext that looks like the result of Base85 encoding. When trying to decode, the result is an error because the format used is not standard Base85, but RFC Base85 (Python version of Base85 b85decode) which has a different character set from regular Ascii85/Base85.

Based on the hint `pydoc3 base64` , it is known that Python provides a decoder for **RFC Base85** via the `base64.b85decode()` function.

To decode Base85, use the following script:

```python
import base64

s = "Nm@rmLsBy{Nm5u-K{iZKPgPMzS2I*lPc%_SMOjQ#O;uV{MM*?PPFhk|Hd;hVPFhq{HaAH<"
print(base64.b85decode(s).decode().strip())
```
Output:

```txt
IZGECR33IRXSA6LPOUQGW3TPO4QGEYLTMUZTEIDFNZRW6ZDJNZTT67I=
```

These results look like ciphertext encoded **Base32**. Then Base32 decoding is carried out on the output.

```python
import base64

s = "Nm@rmLsBy{Nm5u-K{iZKPgPMzS2I*lPc%_SMOjQ#O;uV{MM*?PPFhk|Hd;hVPFhq{HaAH<"
decoded_b85 = base64.b85decode(s).decode().strip()
print(base64.b32decode(decoded_b85).decode().strip())
```

Output:

```txt
FLAG{Do you know base32 encoding?}
```

---

## Flags

```txt
FLAG{Do you know base32 encoding?}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/not-hard/  

