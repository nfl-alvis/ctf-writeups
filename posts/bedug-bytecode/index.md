# Bedug Bytecode — Writeup



{{< admonition tldr "TL;DR" >}}

**Bedug Bytecode — Writeup** is a misc challenge from **OCRY CTF**. This challenge comes from **RAMADHANCTF** with the title *Bedug Bytecode*. The goal is to create an “emoji program” that produces a specific output for the server to return a flag.

{{< /admonition >}}

This challenge comes from **RAMADHANCTF** with the title *Bedug Bytecode*. The goal is to create an “emoji program” that produces a specific output for the server to return a flag.

---

## 1. Recon and Source Analysis

From the main page you can see two important JavaScript files:

* `/static/vm.js` * `/static/app.js` ## Technical Analysis vm.js

In `vm.js` there are the following constants:

```js
const TARGET = "BEDUG_GACOR_1447";
```

Then there is a mapping of emoji opcodes to characters:

| Emojis | Outputs |
| ----- | ------ |
| 🥁 | B |
| 🕋 | E |
| 🌙 | D |
| 🍯 | U |
| 📿 | G |
| ⚡ | _ |
| 🔥 | A |
| 🌟 | C |
| 🎯 | O |
| 🧿 | R |
| 🪙 | 1 |
| 🪔 | 4 |
| 🧾 | 7 |

How the `simulateProgram` function works:

1. Remove whitespace from input.
2. Make sure the program ends with `✅` .
3. Convert each emoji into a character according to the mapping.
4. Compare the results with `TARGET` .

If the conversion result is equal to `BEDUG_GACOR_1447` , then `solved` has the value `true` .

---

## Technical Analysis app.js

When the form is submitted, there is a request to the backend:

```js
fetch("/api/run", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ program })
});
```

Meaning:

* Initial validation is performed locally in the browser.
* Final verification and flag sending is done by the server via the endpoint:

```
POST /api/run
```

---

## 2. Completion Strategy

Since the `TARGET` value is clearly visible in the source code, we simply:

1. Change every character in `BEDUG_GACOR_1447` to emoji according to the opcode table.
2. Add `✅` at the end of the program.

---

## 3. Program preparation

Targets:

```
BEDUG_GACOR_1447
```

Character-by-character conversion:

```
B  → 🥁
E  → 🕋
D  → 🌙
U  → 🍯
G  → 📿
_  → ⚡
G  → 📿
A  → 🔥
C  → 🌟
O  → 🎯
R  → 🧿
_  → ⚡
1  → 🪙
4  → 🪔
4  → 🪔
7  → 🧾
```

Add finalization opcode at the end:

```
✅
```

---

## 4. Final Payload

The correct emoji program is:

```
🥁🕋🌙🍯📿⚡📿🔥🌟🎯🧿⚡🪙🪔🪔🧾✅
```

---

## 5. Exploit Using curl

Because there is a backend endpoint available, we can directly send requests without going through the browser:

```bash
curl -X POST http://TARGET/api/run \
  -H "Content-Type: application/json; charset=utf-8" \
  --data-binary '{"program":"🥁🕋🌙🍯📿⚡📿🔥🌟🎯🧿⚡🪙🪔🪔🧾✅"}'
```

If true, the server will return a response like:

```json
{
  "result": "BEDUG_GACOR_1447",
  "solved": true,
  "flag": "RAMADHANCTF{...}"
}
```

---

## 6. Conclusion

This challenge includes the categories:

* Reverse Engineering (easy)
* Source Code Analysis
* Client-side Logic Inspection

Bottom line:

* The VM only maps emojis to strings.
* TARGET is clearly visible in the source code.
* No obfuscation or additional protection.
* The solution is simply to rearrange the strings according to the mapping.

Once the payload is sent to `/api/run` , a flag will be returned by the server.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/bedug-bytecode/  

