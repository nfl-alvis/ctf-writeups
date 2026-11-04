# Double



{{< admonition tldr "TL;DR" >}}

**double** is a crypto challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

---

## Challenge Description

The challenge provides a ciphertext that looks like a combination of numbers and letters, with a hint:

- “At first it looked like hexadecimal numbers & letters…”
- “After opening, there is still a classic layer with a shift of +7.”

From this description it can be concluded that the message uses **two layers of encoding**:

1. Data is disguised in a form that resembles **hex**
2. After opening, the results are still encrypted with **Caesar/ROT shift +7**

---

## Solution Steps

### 1. Identify the First Layer (Hex)

Ciphertext:

```
526i645552587444556j6j515645396653564l66526j564m58304h565646394451553566516i566656464h4h5130745h587h49774k6h55774m54493166513k3k0h
```

At first glance it looks like hex, but there are non-hex characters such as:

- `i j k l m h` This indicates that the ciphertext is actually a **hex that has been shifted** so that the letters `a-f` change to other letters.

---

### 2. Decode Using CyberChef

So that the process is fast and consistent, decoding is done using **CyberChef**.

Recipe used:

1. `ROT13` (with negative amount to shift letters back)
2. `From Hex` 3. `From Base64` In this challenge the configuration used is:

- ROT13 amount: `-7` - From Hex: delimiter `Auto` - From Base64: default alphabet

The final result immediately produces a plaintext flag.

{{< image src="./images/solved.png" >}}

---

## Flags

```
FGTE{Redacted}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/double/  

