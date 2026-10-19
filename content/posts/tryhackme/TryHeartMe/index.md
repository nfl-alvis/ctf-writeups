---
title: "TryHeartMe"
date: 2026-10-19
categories:
  - Web
tags:
  - JWT
  - Signature Stripping Attack
collections:
  - TryHackMe
---

{{< image src="./images/room_image.webp" >}}

> **Access the hidden item in this Valentine's gift shop.**

{{< admonition tldr "TL;DR" >}}
TryHeartMe is an easy TryHackMe web challenge that demonstrates a JWT signature-stripping vulnerability. By modifying the JWT payload to change the `role` claim from `user` to `admin` and removing the signature, it is possible to escalate privileges, gain access to the admin dashboard, and purchase the hidden ValenFlag item.
{{< /admonition >}}

## Scenario

The challenge description provides us with the following message:

> My Dearest Hacker,  
> The TryHeartMe shop is open for business. Can you find a way to purchase the hidden “Valenflag” item?  
> You can access the web app here: `http://MACHINE_IP:5000`

{{< image src="./images/description_message.webp" >}}

## Initial Reconnaissance

After opening the provided URL, we are greeted by the **TryHeartMe Valentine's Shop**, a web application where users can browse and purchase items.

{{< image src="./images/first-visit.webp" >}}

When attempting to purchase an item, we are prompted to log in first. I created an account and signed in to explore the application's functionality.

After logging in, I noticed that my account had a balance of zero credits, which prevented me from purchasing anything. Attempting to buy an item resulted in the following error message:

> Not enough credits. Online top-ups are currently disabled.

{{< image src="./images/reject-buy.webp" >}}

## Analyzing the JWT

While exploring the application, I noticed the **Role: user** badge displayed in the interface. This caught my attention and prompted me to inspect the browser's cookies.

{{< image src="./images/cookie.webp" >}}

The session cookie turned out to be a JSON Web Token (JWT). Its structure looked like this:

```text
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6IjFAMS4xIiwicm9sZSI6InVzZXIiLCJjcmVkaXRzIjowLCJpYXQiOjE3OTIzNzgzMzcsInRoZW1lIjoidmFsZW50aW5lIn0.k50WLenwMhmnNS9fuGdFG-PeQ2GESJovgZZ2o0fyGzU
```

{{< image src="./images/jwt-debugger.webp" >}}

To make inspecting and modifying the token easier, I used the [Token.dev JWT Debugger](https://www.token.dev).

After decoding the JWT, I found that its payload contained several claims, including `email`, `role`, and `credits`. The `role` claim was set to `user`, while `credits` was set to `0`.

This raised the possibility that the application might be trusting client-controlled JWT claims without properly validating the token's signature.

## Exploiting Signature Stripping

To test this hypothesis, I changed the `role` claim in the JWT payload from `user` to `admin`.

Instead of generating a valid signature, I removed the signature entirely, leaving only the header and payload, followed by a trailing dot.

The modified JWT looked like this:

```text
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6IjFAMS4xIiwicm9sZSI6ImFkbWluIiwiY3JlZGl0cyI6MCwiaWF0IjoxNzkyMzc4MzM3LCJ0aGVtZSI6InZhbGVudGluZSJ9.
```

This was a test for a potential **JWT signature-stripping vulnerability**: if the server accepted the modified token without verifying its signature, it could allow an attacker to tamper with claims such as the user's role.

I replaced the original session cookie with the modified JWT and refreshed the page.

Surprisingly, the application accepted the modified token and granted me administrative privileges. My account also displayed a balance of **5,000 credits**.

{{< image src="./images/admin-privesc.webp" >}}

This confirmed that the application was vulnerable to JWT tampering because it accepted a token with a modified role claim and no signature.

## Accessing the Admin Dashboard

After successfully escalating my privileges, a new **Admin** option appeared in the navigation bar.

Clicking it redirected me to the following endpoint:

`http://MACHINE_IP:5000/admin`

{{< image src="./images/admin-dashboard.webp" >}}

The admin dashboard revealed a product called **ValenFlag**, accompanied by the following description:

> Staff-only purchase  
> The ValenFlag item is visible to staff and prints a voucher receipt.

This indicated that the hidden item was restricted to users with elevated privileges.

## Retrieving the Flag

With administrative access, I clicked **Open ValenFlag**, which redirected me to:

`http://MACHINE_IP:5000/product/valenflag`

I then clicked the **Buy** button on the product page.

The purchase succeeded, and the application displayed the flag.

{{< image src="./images/flag-found.webp" >}}

## Conclusion

This challenge demonstrated how improper JWT signature validation can lead to privilege escalation.

By modifying the `role` claim from `user` to `admin` and removing the token's signature, I was able to bypass the application's intended access controls, obtain administrative privileges, and purchase the hidden ValenFlag item.

**Key takeaway:** JWT signatures must always be validated server-side using an explicitly configured algorithm. Applications should reject unsigned tokens unless they are intentionally supported by a secure design, and authorization decisions must never rely on unverified JWT claims.
