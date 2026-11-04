---
title: "Player Handout - Abyss Jail"
date: 2026-03-01
categories:
  - Pwn
tags:
  - Pwn
  - OCRY CTF
collections:
  - OCRY CTF
---


{{< admonition tldr "TL;DR" >}}

**Player Handout - Abyss Jail** is a challenge from **OCRY CTF**. - `abyss_jail` : binary challenge (ELF 64-bit, PIE).

{{< /admonition >}}

## Files

- `abyss_jail` : binary challenge (ELF 64-bit, PIE).

## Remote

- `nc <HOST> 342` ## Objectives

Get interactive shell from binary, then read `flag.txt` from restricted shell.

## Notes

- Challenge does not run as root.
- The intended path requires a combination of memory bug + bypass command in the shell.
