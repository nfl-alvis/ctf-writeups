# Fools Mate


{{< image src="./images/room_image.webp" >}}

> **Can you bypass the engine?**

{{< admonition tldr "TL;DR" >}}
Fool’s Mate is an easy TryHackMe web challenge that demonstrates why client-side security controls cannot be trusted. Although the application prevents a winning chess move in the browser, the backend API still accepts the request when it is modified and sent manually through Burp Suite.
{{< /admonition >}}

## Initial Analysis

The target is a web-based chess endgame trainer. When opening the application, we are presented with a chessboard and instructed to deliver checkmate in one move.

{{< image src="./images/first-visit.webp" >}}

The white rook on `a1` can move to `a8`, delivering checkmate to the black king on `g8`. However, attempting this move through the web interface triggers a warning popup instead of allowing the move.

{{< image src="./images/popup.webp" >}}

The message indicates that the restriction is enforced by the client-side application. This suggests that the move may still be accepted if the underlying API request is modified directly.

{{< style "text-align:center" >}}
{{< image src="./images/meme-emoji.gif" width="60%" align="center" >}}
{{< /style >}}

## Intercepting a Valid Move

To inspect how moves are submitted, I enabled Burp Suite interception and moved the rook to another valid square. This produced the following request:

{{< image src="./images/request-burpsuite.webp" >}}

```http
POST /api/move HTTP/1.1
Host: 10.112.144.55
Content-Type: application/json

{
  "from": "a1",
  "to": "b1"
}
````

The application sends the source and destination squares as JSON values. The browser blocks the checkmating move, but the server endpoint itself can still be tested manually.

## Bypassing the Client-Side Restriction

I changed the destination square from `b1` to `a8`, then forwarded the modified request:

```json
{
  "from": "a1",
  "to": "a8"
}
```

The server accepted the move and returned a successful response indicating that the position was checkmate. The flag was included in the response body.

{{< image src="./images/response-burpsuite.webp" >}}

## Key Takeaway

The application relied on a client-side restriction to prevent the winning move. Since browser-side JavaScript can be modified or bypassed by an attacker, it must never be treated as a security boundary.

Security-sensitive validation should always be implemented on the server side. In this case, the backend should independently validate whether a move is allowed before returning the game result or exposing the flag.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://nfl-alvis.github.io/ctf-writeups/posts/fools-mate/  

