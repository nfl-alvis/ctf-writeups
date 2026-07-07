# W1seguy


{{< image src="./images/room_image.webp" >}}

> **A w1se guy 0nce said, the answer is usually as plain as day.**

{{< admonition tldr "TL;DR" >}}
W1seguy is an introductory TryHackMe cryptography challenge that demonstrates a weakness in repeating-key XOR encryption. Because part of the plaintext is known, a Known-Plaintext Attack can be used to recover the encryption key and decrypt the provided ciphertext.
{{< /admonition >}}

In this challenge, we are given the server source code and informed that the service is listening on TCP port `1337`.

After connecting to the server with Netcat, it returns an XOR-encrypted value and asks for the encryption key. Submitting the correct key reveals the second flag.

{{< image src="./images/first-conn.webp" >}}

Before interacting further with the service, we can inspect the provided source code.

```python
import random
import socketserver
import socket, os
import string

flag = open('flag.txt','r').read().strip()

def send_message(server, message):
    enc = message.encode()
    server.send(enc)

def setup(server, key):
    flag = 'THM{thisisafakeflag}'
    xored = ""

    for i in range(0, len(flag)):
        xored += chr(ord(flag[i]) ^ ord(key[i % len(key)]))

    hex_encoded = xored.encode().hex()
    return hex_encoded

def start(server):
    res = ''.join(random.choices(string.ascii_letters + string.digits, k=5))
    key = str(res)
    hex_encoded = setup(server, key)
    send_message(server, "This XOR encoded text has flag 1: " + hex_encoded + "\n")

    send_message(server, "What is the encryption key? ")
    key_answer = server.recv(4096).decode().strip()

    try:
        if key_answer == key:
            send_message(server, "Congrats! That is the correct key! Here is flag 2: " + flag + "\n")
            server.close()
        else:
            send_message(server, "Close but no cigar\n")
            server.close()
    except:
        send_message(server, "Something went wrong. Please try again. :)\n")
        server.close()

class RequestHandler(socketserver.BaseRequestHandler):
    def handle(self):
        start(self.request)

if __name__ == '__main__':
    socketserver.ThreadingTCPServer.allow_reuse_address = True
    server = socketserver.ThreadingTCPServer(('0.0.0.0', 1337), RequestHandler)
    server.serve_forever()
```

## Source Code Analysis

The server encrypts a known plaintext using **repeating-key XOR**. The plaintext used to generate the first ciphertext is hardcoded in the `setup()` function:

```python
flag = 'THM{thisisafakeflag}'
```

The encryption key is randomly generated using alphanumeric characters and has a fixed length of five characters:

```python
res = ''.join(random.choices(string.ascii_letters + string.digits, k=5))
key = str(res)
```

The following expression is responsible for repeating the key:

```python
key[i % len(key)]
```

The modulo operator (`%`) resets the key index to the beginning once it reaches the end of the key. Since the key length is five bytes, it is repeated across the plaintext as follows:

```text
Plaintext : T  H  M  {  t  h  i  s  i  s  a  f  a  k  e  f  l  a  g  }
Key       : K0 K1 K2 K3 K4 K0 K1 K2 K3 K4 K0 K1 K2 K3 K4 K0 K1 K2 K3 K4
```

Each plaintext byte is XORed with the corresponding key byte:

```python
xored += chr(ord(flag[i]) ^ ord(key[i % len(key)]))
```

Finally, the XOR output is converted into hexadecimal before being sent to the client:

```python
hex_encoded = xored.encode().hex()
```

Therefore, the received ciphertext must first be decoded from hexadecimal into raw bytes before XOR operations can be performed.

## Known-Plaintext Attack

For XOR encryption, the relationship between plaintext, ciphertext, and key is:

$$
C_i = P_i \oplus K_i
$$

Where:

- $C_i$ is the ciphertext byte at position $i$,
- $P_i$ is the plaintext byte at position $i$,
- $K_i$ is the key byte at position $i$,
- $\oplus$ represents the XOR operation.

Because XOR is reversible, the key can be recovered when both the plaintext and ciphertext are known:

$$
K_i = C_i \oplus P_i
$$

In this challenge, the plaintext is known because the fake flag is hardcoded in the source code:

```text
THM{thisisafakeflag}
```

The first four plaintext bytes are:

```text
T H M {
```

These bytes correspond directly to the first four ciphertext bytes. Therefore, the first four key bytes can be recovered with:

$$
K_0 = C_0 \oplus \texttt{T}
$$

$$
K_1 = C_1 \oplus \texttt{H}
$$

$$
K_2 = C_2 \oplus \texttt{M}
$$

$$
K_3 = C_3 \oplus \texttt{\{}
$$

The ciphertext received from the server can be decoded and XORed with the known plaintext prefix:

```python
>>> encrypted_flag = bytes.fromhex(
...     "6504222a45002d033f4174341b104145780c3a5670221d62545d0016396043381661404334202348"
... )
>>> known_plaintext = b"THM{"
>>> recovered_key = bytes(a ^ b for a, b in zip(encrypted_flag, known_plaintext))
>>> print(recovered_key)
b'1LoQ'
```

At this point, four of the five key bytes have been recovered:

| **Key** | K0 | K1 | K2 | K3 | K4 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Recovered** | 1 | L | o | q | ? |

{.table-responsive}

## Recovering the Final Key Byte

The final key byte, $K_4$, cannot be recovered from the `THM{` prefix because that prefix only contains four bytes.

However, the complete known plaintext ends with the closing brace character, `}`. We can also verify that the ciphertext has a length of 40 bytes:

```python
>>> len(encrypted_flag)
40
```

Since the key length is five bytes, ciphertext positions that correspond to the fifth key byte satisfy:

$$
i \bmod 5 = 4
$$

The final ciphertext byte is at index `39`. Therefore:

$$
39 \bmod 5 = 4
$$

This means the final ciphertext byte was encrypted using $K_4$. Since the final plaintext byte is known to be `}`, the missing key byte can be recovered as follows:

```python
>>> last_key_byte = bytes([encrypted_flag[-1] ^ ord("}")])
>>> print(last_key_byte)
b'5'
```

The complete encryption key is therefore:

```text
1LoQ5
```

{{< style "text-align:center" >}}
{{< image src="./images/maxim-yambor-yambor.gif " width="60%" align="center" >}}
{{< /style >}}

## Decrypting Flag 1

Now that the full key has been recovered, the ciphertext can be decrypted by repeating the key across the encrypted bytes:

```python
>>> full_key = b"1LoQ5"
>>> plaintext = bytes(
...     encrypted_flag[i] ^ full_key[i % len(full_key)]
...     for i in range(len(encrypted_flag))
... )
>>> print(plaintext.decode())
THM{thisisafakeflag}
```

{{< image src="./images/flag1-decrypted.webp" >}}

## Retrieving Flag 2

Finally, submitting the recovered key to the server reveals the second flag.

{{< image src="./images/got-flag2.webp" >}}

This challenge demonstrates why repeating-key XOR should not be used for encryption. When attackers know or can guess even a portion of the plaintext, they can recover the corresponding key bytes and potentially decrypt the entire message.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://nfl-alvis.github.io/ctf-writeups/posts/w1seguy/  

