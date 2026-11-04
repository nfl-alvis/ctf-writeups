# A CAPture of a Flag



> **This isn't what I had in mind when I asked someone to capture a flag... can you help? You should check out Wireshark.**

{{< admonition tldr "TL;DR" >}}

**A CAPture of a Flag** is a foundational network forensics challenge involving packet capture analysis. By following HTTP TCP streams in Wireshark, we locate an unencrypted HTTP GET request carrying a base64-encoded parameter ( `msg=ZmxhZ3tBRmxhZ0luUENBUH0=` ). Decoding this string reveals the hidden flag.

{{< /admonition >}}

## Initial Reconnaissance

We are provided with a file named `flag` . We first determine the file type:

```bash
file flag
```

```text
flag: pcapng capture file - version 1.0
```

The file is a standard **PCAPNG** network capture recording network activity.

---

## Traffic Analysis with Wireshark

We load the packet capture into **Wireshark** to inspect communication protocols.

Filtering by `http` or following the TCP stream on port 80 reveals several web requests. Examining **TCP Stream 5** exposes an interesting HTTP GET request:

{{< image src="./images/wireshark.webp" >}}

```http
GET /?msg=ZmxhZ3tBRmxhZ0luUENBUH0= HTTP/1.1
Host: www.hazzy.co.uk
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:48.0) Gecko/20100101 Firefox/48.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Upgrade-Insecure-Requests: 1
```

The request queries the root path with a parameter named `msg` . The value:

```text
ZmxhZ3tBRmxhZ0luUENBUH0=
```

has the characteristics of standard **Base64** encoding (alphanumeric characters ending with an `=` padding byte).

---

## Decoding the Payload

We decode the extracted Base64 payload in the terminal:

```bash
echo "ZmxhZ3tBRmxhZ0luUENBUH0=" | base64 -d
```

```text
flag{AFlagInPCAP}
```

---

## Flags

```text
flag{AFlagInPCAP}
```

---

## Command-Line Alternative (tshark)

We can achieve the exact same extraction directly from the command line using `tshark` :

```bash
tshark -r flag -Y "http.request.uri contains msg" -T fields -e http.request.uri | \
  grep -oP 'msg=\K[^&]+' | base64 -d
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/a-capture-of-a-flag/  

