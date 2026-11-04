---
title: "Hextroadinary"
date: 2026-02-22
categories:
  - Crypto
tags:
  - Crypto
  - CTFlearn
collections:
  - CTFlearn
---


{{< admonition tldr "TL;DR" >}}

**Hextroadinary** is a crypto challenge from **CTFlearn**. Meet ROXy, a coder obsessed with being exclusively the world's best hacker. She specializes in short cryptic hard to decipher secret codes. The below hex values ​​for example, she did something with them to generate a secret code, can you figure out what? Your answer should start with 0x.

{{< /admonition >}}

## Description

Meet ROXy, a coder obsessed with being exclusively the world's best hacker. She specializes in short cryptic hard to decipher secret codes. The below hex values ​​for example, she did something with them to generate a secret code, can you figure out what? Your answer should start with 0x.

0xc4115 0x4cf8

---

## Technical Analysis

Given two numbers in hexadecimal format. In the challenge description there is the word **"ROX"**, which if reversed forms the word **"XOR"**. This is a strong indication that the operation used is **bitwise XOR**.

---

## Decode Process

Assuming that the two hexadecimal values are XORed with each other, the calculation is carried out using the web [xor.pw](https://xor.pw/#):

{{< image src="./images/Decoded.png" >}}

Because in the question we are told to add `0x` at the beginning of the flag and the final flag is `0xc0ded` .

---

## Flags

```0xc0ded```
