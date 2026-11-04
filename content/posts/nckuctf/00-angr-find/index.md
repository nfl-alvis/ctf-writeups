---
title: "Write-up Reverse Engineering - `00angrfind`"
date: 2026-05-01
categories:
  - Misc
tags:
  - Misc
  - NCKUCTF
collections:
  - NCKUCTF
---



{{< admonition tldr "TL;DR" >}}

**Write-up Reverse Engineering - `00angrfind` ** is a misc challenge from **NCKUCTF**. This challenge asks us to find the correct password to generate a flag with the format:

{{< /admonition >}}

## Summary

This challenge asks us to find the correct password to generate a flag with the format:

```text
NCKUCTF{password}
```

Based on the results of reverse engineering on the `00_angr_find` binary, the correct password is:

```text
JXWVXRKX
```

So the final flag is:

```text
NCKUCTF{JXWVXRKX}
```

## 1. File Identification

The first step is to identify the binary file type:

```bash
file 00_angr_find
```

Important results:

```text
ELF 32-bit LSB executable, Intel i386, dynamically linked, not stripped
```

Interpretation:

- The file is a 32-bit ELF binary for the `i386` architecture.
- Function symbols are still available due to binary `not stripped` .
- This really helps the static analysis process because function names like `main` and `complex_function` are still visible.

## 2. Important String Enumeration

Next, perform a string enumeration on the binary:

```bash
strings -a -n 4 00_angr_find
```

Relevant strings:

```text
Try again.
Enter the password:
JACEJGCS
Good Job.
complex_function
main
```

From here you can see one very suspicious string, namely:

```text
JACEJGCS
```

However, this string is not necessarily the original password. It could be that the string is the result of a transformation of user input.

## 3. Function Analysis `main` The disassembly function `main` shows the program flow as follows:

1. The program displays a prompt:

```text
   Enter the password:
   ```

2. The program reads user input in the format:

```c
   scanf("%8s", buffer);
   ```

3. The program iterates over 8 input characters.

4. Each input character is sent to the `complex_function` function along with the index of that character.

5. The transformation results are saved back to the input buffer.

6. After all 8 characters have been processed, the buffer is compared to a constant string:

```text
   JACEJGCS
   ```

7. If equal, the program prints:

```text
   Good Job.
   ```

The important pieces of logic from `main` can be summarized as:

```c
for (i = 0; i <= 7; i++) {
    input[i] = complex_function(input[i], i);
}

if (strcmp(input, "JACEJGCS") == 0) {
    puts("Good Job.");
} else {
    puts("Try again.");
}
```

Conclusion at this stage:

- The original password is not directly `JACEJGCS` .
- `JACEJGCS` is the final result after user input is processed by `complex_function` .

## 4. Function Analysis `complex_function` Disassembly of the `complex_function` function shows two important parts:

### 4.1 Character Validation

The function checks whether the input character is in the range:

```text
'A' sampai 'Z'
```

If a character is outside the uppercase range, the program immediately displays:

```text
Try again.
```

and get out.

This means that the password only consists of capital letters `A-Z` .

### 4.2 Character Transformation

After validation, the function performs mathematical operations on the input characters and their indices.

If simplified, the formula is:

```text
out[i] = ((in[i] - 'A') + 3*i) mod 26 + 'A'
```

Description:

- `in[i]` is the `i` th input character 
- `out[i]` is the result of the transformation
- `i` is the character index, starting from `0` Thus:

- 0th character is shifted `0` - 1st character is shifted `3` - 2nd character shifted `6` - 3rd character shifted `9` - and so on

## 5. Reverse Transformation

Because the final result should be the same as:

```text
JACEJGCS
```

then we just reverse the transformation formula:

```text
in[i] = ((out[i] - 'A') - 3*i) mod 26 + 'A'
```

We count them one by one.

### 5.1 Calculation Table

| Index | Target `out[i]` | Shift `3*i` | Results `in[i]` |
|------:|---------|------------:|--------------------|
| 0 | J | 0 | J |
| 1 | A | 3 | X |
| 2 | C | 6 | W |
| 3 | E | 9 | V |
| 4 | J | 12 | X |
| 5 | G | 15 | R |
| 6 | C | 18 | K |
| 7 | S | 21 | X |

So the password obtained is:

```text
JXWVXRKX
```

## 6. Verify

We verify by reapplying the forward formula:

```text
out[i] = ((in[i] - 'A') + 3*i) mod 26 + 'A'
```

For input:

```text
JXWVXRKX
```

the result is:

```text
JACEJGCS
```

Because the transformation result matches the comparison string in binary, the password is valid.

## 7. Final Flags

```text
NCKUCTF{JXWVXRKX}
```

## 8. Conclusion

This challenge is basic reverse engineering with the pattern:

- identify important strings
- validation function analysis
- understanding character transformation
- inversion of the formula to obtain the original input

Because the binary is still `not stripped` , this challenge can be solved efficiently using static analysis without the need for symbolic execution or dynamic debugging.
