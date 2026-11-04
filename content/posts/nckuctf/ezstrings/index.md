---
title: "EzStrings"
date: 2026-04-26
categories:
  - Reverse
tags:
  - Reverse Engineering
  - ELF Binary
  - Strings
  - Static Analysis
collections:
  - NCKUCTF
---



> **An introductory reverse engineering challenge providing a Linux ELF executable. Can you locate the hardcoded secret?**

{{< admonition tldr "TL;DR" >}}

**EzStrings** is an introductory binary analysis challenge. The binary is an unstripped 64-bit ELF executable. Running `strings` against the binary reveals the flag stored in plaintext within the `.rodata` section without encryption or obfuscation.

{{< /admonition >}}

## Binary Triage

We are provided with a single binary file named `EzStrings` . We begin by determining its file type and compilation properties:

```bash
file EzStrings
```

```text
EzStrings: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=a1b2c3d4..., for GNU/Linux 3.2.0, not stripped
```

Key characteristics:
- **Architecture:** x86-64 Linux binary.
- **Symbols:** `not stripped` (function names and variable symbols are preserved).
- **Format:** ELF 64-bit executable.

---

## Static Analysis

Because the binary is unstripped and simple, our first step before launching full decompilation in Ghidra or IDA Pro is running the `strings` utility to extract printable character sequences:

```bash
strings EzStrings | grep -i "NCKUCTF"
```

Output:

```text
NCKUCTF{0n1y_t00k_m3_1_sec_7o_g3t_th3_f149...}
```

The string is located directly in the read-only data ( `.rodata` ) section of the binary.

---

## Binary Inspection (objdump)

To understand how the binary utilizes the string, we can disassemble the `main` function using `objdump` :

```bash
objdump -d -M intel EzStrings | grep -A 15 "<main>:"
```

```nasm
0000000000001149 <main>:
    1149: push   rbp
    114a: mov    rbp,rsp
    114d: sub    rsp,0x10
    1151: lea    rax,[rip+0xeb0]        # 2008 <_IO_stdin_used+0x8>
    1158: mov    rdi,rax
    115b: call   1030 <puts@plt>
    1160: mov    eax,0x0
    1165: leave  
    1166: ret
```

The assembly confirms that `main` simply loads the address of the string literal at offset `0x2008` into `rdi` and invokes `puts()` . The binary performs no string decryption, XOR scrambling, or dynamic generation at runtime.

---

##Flags

```text
NCKUCTF{0n1y_t00k_m3_1_sec_7o_g3t_th3_f149...}
```

---

## Key Takeaways

1. **Avoid Hardcoded Plaintext Secrets:** Sensitive data, encryption keys, and secrets embedded directly in binaries can be extracted with basic static inspection tools like `strings` .
2. **Binary Hardening:** For production software where intellectual property or proprietary logic must be protected, compile with stripping ( `strip --strip-all` ) and implement secure key management rather than relying on embedded constants.
