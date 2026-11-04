# Hide and Seek


{{< image src="./images/solve.webp" >}}

> **Can you see me? I'm so close to you, but you can't see me.**

{{< admonition tldr "TL;DR" >}}
**Hide and Seek** is an introductory web challenge testing fundamental source code review. The flag is embedded directly within the homepage HTML source code, hidden from the graphical viewport using styling or whitespace comments. Viewing the raw DOM source exposes the secret.
{{< /admonition >}}

## Analysis

The target application links to the root webpage of the CTF site:

```text
https://ctf.hackme.quest/
```

The challenge prompt suggests that the flag is present in plain sight but visually hidden on the rendered page.

---

## Solution

We fetch the raw HTML document using `curl` or inspect the page source using the browser shortcut `Ctrl+U`:

```bash
curl -s "https://ctf.hackme.quest/" | grep -i "FLAG{"
```

Output:

```html
<!-- FLAG{0h U C meeeeeeeeeeeeeeeeeeee!} -->
```

The flag is positioned within an unrendered HTML comment block in the markup.

---

## Flag

```text
FLAG{0h U C meeeeeeeeeeeeeeeeeeee!}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/hide-and-seek/  

