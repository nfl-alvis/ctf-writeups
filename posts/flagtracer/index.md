# FlagTracer




{{< admonition tldr "TL;DR" >}}

**FlagTracer** is a reverse challenge from **NCKUCTF**. * **Challenge Name** : FlagTracer * **Category** : REVERSE * **Organizer** : NCKUCTF * **Writeup Author** : spawn2pwn

{{< /admonition >}}

## **1. Identity Challenge**

* **Challenge Name** : FlagTracer
* **Category** : REVERSE
* **Organizer** : NCKUCTF
* **Writeup Writer** : spawn2pwn

---

## **2. Problem Description**

This challenge provides a 64-bit ELF binary file that requests flag input from the user.

The program will verify the input and display whether the flag is true or false.

Main goals:

> **Finding the correct flag by analyzing the validation mechanism in the binary**

---

## **3. System Analysis**

Initial binary information:

```bash id="s2a1d3"
file FlagTracer
```

Output:

```id="x9p2k1"
ELF 64-bit LSB pie executable, x86-64, dynamically linked, not stripped
```

###Characteristics:

1. **ELF 64-bit** (Linux binary)
2. **PIE enabled**
3. **Dynamically linked**
4. **Not stripped** (symbols still available)

---

## **4. Identify Vulnerabilities**

The program asks for input and then checks using the string function.

From the observation results, validation is most likely carried out by:

```c
strcmp(user_input, FLAG)
```

The primary vulnerability:

> **Sensitive data (flags) can be seen through dynamic tracing**

Because the binary uses library functions such as `strcmp` , the parameters used can be inspected using the tracing tool.

---

## **5. Exploitation Methodology**

### **5.1 Running the Program**

```bash id="k1l2m3"
./FlagTracer
```

Output:

```id="z8x7c6"
Enter flag: test
Wrong!
```

---

### **5.2 Analysis with ltrace**

Using `ltrace` To view library function calls:

```bash id="p9o8i7"
ltrace ./FlagTracer
```

Results:

```id="q1w2e3"
strcmp("test", "NCKUCTF{U5ing_l7race_c4n_3asily_"...) = 38
```

It can be seen that the `strcmp` function compares the input with the flag string.

---

### **5.3 Displaying Longer Strings**

To view the flag in full:

```bash id="a4s5d6"
ltrace -s 100 ./FlagTracer
```

---

### **5.4 Flag Extraction**

Output:

```id="f7g8h9"
strcmp("test", "NCKUCTF{U5ing_l7race_c4n_3asily_g3t_7he_f1ag!}") = 38
```

From here you can get the flag:

```id="j1k2l3"
NCKUCTF{U5ing_l7race_c4n_3asily_g3t_7he_f1ag!}
```

Note:

* There is an additional character `` (bell) at the end of the string
* These characters are not part of the flag

---

## **6. Exploitation Results**

FLAG was successfully obtained:

```id="m4n5b6"
NCKUCTF{U5ing_l7race_c4n_3asily_g3t_7he_f1ag!}
```

---

## **7. Discussion**

The primary vulnerability:

1. **Flags are stored directly in the binary**
2. **Using strcmp() for validation**
3. **No protection against dynamic analysis**

By using `ltrace` , an attacker can:

* View library function parameters
* Identifies the string being compared
* Get the flag without complex reverse engineering

---

## **8. Improvement Recommendations**

### **1. Avoid direct comparisons with flag**

Use hashing method:

```c
hash(input) == hash(flag)
```

---

### **2. Obfuscate string**

* Encryption flag in binary
* Decryption only at runtime

---

### **3. Anti-debugging / anti-tracing**

* Detect use of `ltrace` / `ptrace` * Use anti-analysis techniques

---

## **9. Conclusion**

This challenge shows that:

> **Dynamic tracing is a very effective technique for analyzing simple binaries**

By using:

```bash id="z1x2c3"
ltrace
```

Attackers can:

* View the validation process
* Retrieve flag values directly
* Avoid complex reverse engineering processes

---

## **10. Attachments**

### **Analysis Command**

```bash id="v4b5n6"
file FlagTracer
ltrace ./FlagTracer
ltrace -s 100 ./FlagTracer
```

---

### **ltrace results**

```id="c7v8b9"
strcmp("test", "NCKUCTF{U5ing_l7race_c4n_3asily_g3t_7he_f1ag!}")
```

---

### **FLAG**

```id="n1m2q3"
NCKUCTF{U5ing_l7race_c4n_3asily_g3t_7he_f1ag!}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/flagtracer/  

