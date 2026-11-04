---
title: "Waktu_Adalah_Kunci"
date: 2026-04-08
categories:
  - Crypto
tags:
  - Crypto
  - WMS
collections:
  - WMS
---


{{< admonition tldr "TL;DR" >}}

**Time_Is_Key** is a crypto challenge from **WMS**. Name: Time Is the Key Value: 0 Description: Phantom Collective's KeyServer uses RSA to encrypt confidential documents. From network interception, we get the public key and ciphertext.

{{< /admonition >}}

Name: Timing Is Key
Value: 0
Description: Phantom Collective's KeyServer uses RSA to encrypt confidential documents. From network interception, we get the public key and ciphertext.

The forensic team also managed to extract the boot log from the server. There's something odd about the way they generate cryptographic keys.

Files: <code>intercept.json</code>, <code>server_boot.log</code>
Format flags: <code>WMS{...}</code>
