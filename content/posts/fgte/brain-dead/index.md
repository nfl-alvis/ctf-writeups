---
title: "Brain Dead"
date: 2026-02-22
categories:
  - Misc
tags:
  - Esoteric Languages
  - Brainfuck
  - Ook!
  - Multi-Stage Interpreter
  - Python Scripting
collections:
  - FGTE
---


{{< image src="./images/challenge.webp" >}}

> **A peculiar file filled with punctuation marks holds the key. Can you decipher this multi-layered esoteric program?**

{{< admonition tldr "TL;DR" >}}

**Brain Dead** is an esoteric programming language challenge. The source file `brain_dead.bf` is composed of 2-character compressed **Ook!** symbol pairs ( `.?` , `!!` , `?!` ). Decoding these pairs into Brainfuck and executing them in a Python virtual tape interpreter produces an intermediate script written with custom language tokens. Interpreting this second stage yields decimal ASCII codes, which assemble into the final flag.

{{< /admonition >}}

## 1. Challenge File Inspection

The challenge file `brain_dead.bf` contains sequences composed exclusively of three punctuation characters: `.` , `?` , and `!` :

```text
.?... ..... ..!.? ..... ..!?! ...
```

This character set corresponds directly to **Ook!**, a Brainfuck dialect that uses pairs of punctuation marks.

---

## 2. Deciphering the Grammar

Standard OK! uses words such as `Ook. Ook?` . In this challenge, the words have been compressed into raw punctuation pairs:

| OK! Pair | Brainfuck Instructions | Functions |
| :---: | :---: | :--- |
| `.?` | `>` | Increment memory pointer |
| `?.` | `<` | Decrement memory pointer |
| `..` | `+` | Increment bytes at pointer |
| `!!` | `-` | Decrement byte at pointer |
| `!.` | `.` | Output bytes at pointer |
| `.!` | `,` | Read bytes into pointer |
| `!?` | `[` | Jump forward if pointer byte is zero |
| `?!` | `]` | Jump backward if pointer byte is non-zero |

{.table-responsive}

---

## 3. The Multi-Stage Execution Pipeline

Running the converted Brainfuck code does not immediately reveal plaintext. Instead, the first execution stage emits an intermediate script containing repetitive string tokens:

```text
stage1 output: token-based script
```

These intermediate tokens map back to Brainfuck operations, which in turn generate a series of decimal numbers. Converting those decimal values ​​to their corresponding ASCII characters uncovers the flag.

---

## 4. Complete Automated Solver

We construct a multi-stage Python emulator:

```python
#!/usr/bin/env python3
import re

OOK_MAP = {
    (".", "?"): ">",
    ("?", "."): "<",
    (".", "."): "+",
    ("!", "!"): "-",
    ("!", "."): ".",
    (".", "!"): ",",
    ("!", "?"): "[",
    ("?", "!"): "]",
}

TOKEN_MAP = {
    "zero": ">",
    "one": "<",
    "two": "+",
    "three": "-",
    "four": ".",
    "five": ",",
    "six": "[",
    "seven": "]",
}

def run_brainfuck(code):
    tape = [0] * 30000
    ptr = 0
    pc = 0
    output = []
    brackets = {}
    stack = []

    for i, c in enumerate(code):
        if c == "[":
            stack.append(i)
        elif c == "]":
            start = stack.pop()
            brackets[start] = i
            brackets[i] = start

    while pc < len(code):
        cmd = code[pc]
        if cmd == ">":
            ptr += 1
        elif cmd == "<":
            ptr -= 1
        elif cmd == "+":
            tape[ptr] = (tape[ptr] + 1) & 0xFF
        elif cmd == "-":
            tape[ptr] = (tape[ptr] - 1) & 0xFF
        elif cmd == ".":
            output.append(chr(tape[ptr]))
        elif cmd == "[" and tape[ptr] == 0:
            pc = brackets[pc]
        elif cmd == "]" and tape[ptr] != 0:
            pc = brackets[pc]
        pc += 1

    return "".join(output)

def ook_to_bf(data):
    clean = [c for c in data if c in ".?!"]
    return "".join(
        OOK_MAP[(clean[i], clean[i + 1])] for i in range(0, len(clean), 2)
    )

def tokens_to_bf(s):
    s = s.lower()
    i = 0
    out = []
    tokens = sorted(TOKEN_MAP.keys(), key=len, reverse=True)
    while i < len(s):
        for t in tokens:
            if s.startswith(t, i):
                out.append(TOKEN_MAP[t])
                i += len(t)
                break
        else:
            i += 1
    return "".join(out)

def nums_to_ascii(s):
    nums = list(map(int, re.findall(r"\d+", s)))
    return "".join(map(chr, nums))

if __name__ == "__main__":
    raw_data = open("brain_dead.bf", "r", encoding="utf-8").read()

    # Stage 1: Compressed Ook! -> Brainfuck -> execute
    stage1 = run_brainfuck(ook_to_bf(raw_data))

    # Stage 2: Tokens -> Brainfuck -> execute
    stage2 = run_brainfuck(tokens_to_bf(stage1))

    # Stage 3: Numeric ASCII code conversion
    flag = nums_to_ascii(stage2)
    print("[+] Flag:", flag)
```

Running the script:

```bash
python3 decode.py
```

Output:

```text
[+] Flag: FGTE{BRAIN_GO_BRRR}
```

---

## Flag

```text
FGTE{BRAIN_GO_BRRR}
```
