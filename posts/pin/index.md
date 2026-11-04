# PIN



{{< admonition tldr "TL;DR" >}}

**PIN** is a reverse challenge from **CTFlearn**. Can you crack my pin?   [rev1](https://mega.nz/#!PXYjCKCY!F2gcs83XD6RxjOR-FNWGQZpyvUFvDbuT-PTnqRhBPGQ)

{{< /admonition >}}

##Challenge

Can you crack my pin?  
[rev1](https://mega.nz/#!PXYjCKCY!F2gcs83XD6RxjOR-FNWGQZpyvUFvDbuT-PTnqRhBPGQ)

---

## Technical Analysis

This challenge is a simple **reverse engineering binary ELF**. The program asks for input in the form of **PIN** and will verify whether the value entered is correct or not.  
Running the binary directly:

```bash
./rev1
```

Output:

```
Masukan PIN =
```

This indicates that the program is waiting for numeric input from the user.

---

## Static Technical Analysis (Disassembly)

Binaries are analyzed using **GDB + pwndbg**.
The disassembly function `main` shows the program flow as follows:

```asm
Dump of assembler code for function main:
   0x00000000004005d6 <+0>:     push   rbp
   0x00000000004005d7 <+1>:     mov    rbp,rsp
   0x00000000004005da <+4>:     sub    rsp,0x10
   0x00000000004005de <+8>:     lea    rdi,[rip+0xdf]        # 0x4006c4
   0x00000000004005e5 <+15>:    mov    eax,0x0
   0x00000000004005ea <+20>:    call   0x4004a0 <printf@plt>
   0x00000000004005ef <+25>:    lea    rax,[rbp-0x4]
   0x00000000004005f3 <+29>:    mov    rsi,rax
   0x00000000004005f6 <+32>:    lea    rdi,[rip+0xd6]        # 0x4006d3
   0x00000000004005fd <+39>:    mov    eax,0x0
   0x0000000000400602 <+44>:    call   0x4004b0 <__isoc99_scanf@plt>
   0x0000000000400607 <+49>:    mov    eax,DWORD PTR [rbp-0x4]
   0x000000000040060a <+52>:    mov    edi,eax
   0x000000000040060c <+54>:    call   0x4005b6 <cek>
   0x0000000000400611 <+59>:    test   eax,eax
   0x0000000000400613 <+61>:    je     0x400623 <main+77>
   0x0000000000400615 <+63>:    lea    rdi,[rip+0xba]        # 0x4006d6
   0x000000000040061c <+70>:    call   0x400490 <puts@plt>
   0x0000000000400621 <+75>:    jmp    0x40062f <main+89>
   0x0000000000400623 <+77>:    lea    rdi,[rip+0xba]        # 0x4006e4
   0x000000000040062a <+84>:    call   0x400490 <puts@plt>
   0x000000000040062f <+89>:    mov    eax,0x0
   0x0000000000400634 <+94>:    leave
   0x0000000000400635 <+95>:    ret
```

From this it can be seen that:

1. PIN input is read using `scanf` 2. The PIN value is sent as an argument to the `cek` function 
3. The return value of `cek` determines whether the PIN is correct or incorrect

---

## Technical Analysis Function `cek` Disassembly function `cek` :

```asm
Dump of assembler code for function cek:
   0x00000000004005b6 <+0>:     push   rbp
   0x00000000004005b7 <+1>:     mov    rbp,rsp
   0x00000000004005ba <+4>:     mov    DWORD PTR [rbp-0x4],edi
   0x00000000004005bd <+7>:     mov    eax,DWORD PTR [rip+0x200a7d]        # 0x601040 <valid>
   0x00000000004005c3 <+13>:    cmp    DWORD PTR [rbp-0x4],eax
   0x00000000004005c6 <+16>:    jne    0x4005cf <cek+25>
   0x00000000004005c8 <+18>:    mov    eax,0x1
   0x00000000004005cd <+23>:    jmp    0x4005d4 <cek+30>
   0x00000000004005cf <+25>:    mov    eax,0x0
   0x00000000004005d4 <+30>:    pop    rbp
   0x00000000004005d5 <+31>:    ret
```

The `cek` function simply performs a **direct comparison** between the PIN input and a **global variable** named `valid` .

---

## Retrieves Valid PIN Value

The address of the global variable `valid` is at:

```
0x601040
```

Reading the value in GDB:

```bash
x/w 0x601040
```

Output:

```
0x601040 <valid>: 333333
```

This means that the PIN considered valid by the program is:

```
333333
```

---

## Flags

```text
333333
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/pin/  

