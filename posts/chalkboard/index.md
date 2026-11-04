# Chalkboard




{{< admonition tldr "TL;DR" >}}

**Chalkboard** is a forensics challenge from **CTFlearn**. ---

{{< /admonition >}}

# Chalkboard

---

##Challenge

Solve the equations embedded in the jpeg to find the flag. Solve this problem before solving my Scope challenge which is worth 100 points.  
[Math.jpg](https://ctflearn.com/challenge/download/972)

---

## Technical Analysis

We are provided with an image file named `Math.jpg` .

{{< image src="./images/Math.jpg" >}}

To look for hidden clues, an inspection was performed plaintext string within the image file. From the results, the following information is obtained:

```
The flag for this challenge is of the form:
CTFlearn{I_Like_Math_x_y}

where x and y are the solution to these equations:
3x + 5y = 31
7x + 9y = 59
```

This means that the values `x` and `y` are solutions to a system of linear equations in two variables.

---

## Solving Equations

### Known Equations

```
(1) 3x + 5y = 31
(2) 7x + 9y = 59
```

---

## Methodology 1 – Equating the y coefficients

Multiply equation (1) by 9:

```
27x + 45y = 279
```

Multiply equation (2) by 5:

```
35x + 45y = 295
```

---

## Methodology 2 – Elimination of variables

Subtract the two equations:

```
(35x + 45y) - (27x + 45y) = 295 - 279
```

Results:

```
8x = 16
```

So:

```
x = 2
```

---

## Methodology 3 – Substitution for x Values

Substitute `x = 2` into equation (1):

```
3(2) + 5y = 31
6 + 5y = 31
5y = 25
y = 5
```

---

## Final Results

```
x = 2
y = 5
```

---

##Flags

```text
CTFlearn{I_Like_Math_2_5}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/chalkboard/  

