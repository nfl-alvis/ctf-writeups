---
title: "Secret Agent’s Briefing"
date: 2026-02-22
categories:
  - Misc
tags:
  - Misc
  - FGTE
collections:
  - FGTE
---



{{< admonition tldr "TL;DR" >}}

**Secret Agent's Briefing** is a misc challenge from **FGTE**. ---

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Challenge Description
The challenge provides a PGP encrypted message along with a keypair ( `public_key.asc` and `private_key.asc` ).  
The target is to decrypt `message.asc` to get the flag.

---

## Provided Files
- `memo.txt` - `private_key.asc` - `public_key.asc` - `message.asc` ---

## Initial Analysis
The `message.asc` file is a PGP (ASCII-armored) ciphertext.  
Because the private key is provided, the decryption process should be carried out directly, but the private key is protected with a passphrase.

So the main step in this challenge is to find the passphrase of `private_key.asc` .

Hint is in `memo.txt` :

> This motto often appears at the end of agents' reports investigating unsolved mysteries.

The motto that is very synonymous with the series **The X-Files** is:

> **“Trust No One”**

In the context of passwords/passphrases, the most commonly used variations are:
- `TrustNo1` - `trustno1` - `TrustNoOne` For fast brute force, the wordlist can be narrowed down by taking relevant candidates from `rockyou.txt` .

---

## Exploitation / Solution

### 1. Create a Candidate Wordlist
Instead of brute forcing the full `rockyou.txt` , a small wordlist of variations containing the keyword `trustno` is created.

```bash
grep -i "trustno" /usr/share/wordlists/rockyou.txt > wordlist.txt
```
---

### 2. Extract Hash Passphrase Private Key

John the Ripper needed a hash extracted from the private key.
The tool used is `gpg2john` .

```bash
gpg2john private_key.asc > hash.txt
```

---

### 3. Crack Passphrase with John

Once the hash is available, the passphrase can be searched using the narrowed wordlist.

```bash
john --wordlist=wordlist.txt hash.txt
```

John's output shows the correct passphrase:

```
TrustNo1 (Agent 17)
```

---

### 4. Decrypt PGP Messages

With the passphrase found, the message can be decrypted using `gpg` .

```bash
gpg --batch --yes --pinentry-mode loopback \
  --passphrase 'TrustNo1' \
  --decrypt message.asc
```

Output:

{{< image src="./images/solved.png" >}}

---

##Flags

```
FGTE{REDACTED}
```
