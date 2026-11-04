# IoT Security Research



> **Mira sent her research results from MIRA-NODE to INGGID-HUB. The gateway reported the transfer was successful, but the file never arrived at the receiver's end. The only remaining artifact is `penelitian.pcap` . Trace the communication between the two devices, reconstruct the transmitted data, and find what is hidden within the research findings.**

{{< admonition tldr "TL;DR" >}}

The research file was transmitted as 24 AES-128-CBC encrypted chunks over MQTT. The AES key was wrapped using INGGID-HUB's RSA-1024 public key. Both devices (MIRA-NODE and INGGID-HUB) generated their RSA keys using a **shared prime** due to an outdated demo prototype configuration. Calculating $\gcd(N_1, N_2)$ instantly factors both moduli, recovering INGGID-HUB's private key. After decrypting the AES session key, reassembling the 24 chunks, and unzipping the archive, the flag was extracted from the EXIF ​​ `UserComment` metadata of `dragonfly.jpg` .

{{< /admonition >}}

## 1. Network Reconnaissance (PCAP)

We begin by inspecting the protocol hierarchy and conversation endpoints in `penelitian.pcap` using `tshark` :

```bash
tshark -r penelitian.pcap -q -z io,phs
```

```text
frame   frames:121 bytes:15314
  tcp   frames:108  → mqtt frames:51
  udp   frames:9    → data frames:8, dns frames:1
  icmp  frames:2
  arp   frames:2
```

We identified three distinct hosts active on the lab network `10.13.37.0/24` :

| IP Address | Roles | Description |
| :--- | :--- | :--- |
| `10.13.37.10` | **MQTT Broker** | Standard MQTT service listening on port 1883 |
| `10.13.37.21` | **MIRA-NODE** | Transmitter sending encrypted payload chunks |
| `10.13.37.22` | **INGGID-HUB** | Receiver/Gateway nodes |

{.table-responsive}

UDP packets directed to port 1883 (containing `iot/sensor/temperature {"v":23.x}` ) and DNS queries to `time.lab.local` are background sensor telemetry noise. The critical communication occurs over **MQTT**.

---

## 2. Extracting MQTT Messages

We dump all MQTT publish packets with their topic names and payloads:

```bash
tshark -r penelitian.pcap -Y mqtt -T fields \
  -e frame.number -e ip.src -e mqtt.topic -e mqtt.msg
```

Key topics identified in the stream:

| Frames | Topics | Payload Description |
| :--- | :--- | :--- |
| `53` | `iot/crypto/public` | RSA public key for **MIRA-NODE** ( `n` , `e=65537` ) |
| `68` | `iot/crypto/public` | RSA public key for **INGGID-HUB** ( `n` , `e=65537` ) |
| `57` | `iot/crypto/session` | Encrypted session key ( `TX-4812` , `RSA-1024-RAW-PKCS1` ) |
| `61` | `iot/transfer/meta` | Metadata transfer: filename, cipher, IV, total chunks, SHA-256 |
| `65–120` | `iot/transfer/data/0001..0024` | 24 sequential ciphertext hex chunks |
| `80` | `iot/transfer/ack` | Gateway acknowledgment ( `{"state":"complete","chunks":24}` ) |

{.table-responsive}

A chat log found in `lab/chat/*` yields a critical operational clue:

```text
inggid : The prototype encryption from yesterday is still active.
mira   : Still using the keys from the initial demo?
inggid : Yes, haven't had time to regenerate everything.
inggid : The crypto configuration is inherited directly from the demo, I only changed the device names.
```

Inheriting crypto configuration without regenerating primes strongly suggests that both devices share a common prime factor in their RSA moduli.

Parsing the metadata in frame `61` :

```json
{
  "transfer_id": "TX-4812",
  "sender": "MIRA-NODE",
  "receiver": "INGGID-HUB",
  "filename": "hasil_penelitian.zip",
  "cipher": "AES-128-CBC",
  "iv": "d2597bb0b5d9c63a481ca46228900764",
  "chunks": 24,
  "chunk_size": 213,
  "ciphertext_sha256": "dedb3cdbb3ff31c23a5c1cdc4a94a3e4605b1b2f716f45c32a3a52ced668b516"
}
```

---

## 3. RSA Shared-Prime Factorization Attack (GCD)

Both devices published 1024-bit RSA moduli:
$$N_{\text{mira}} = p \cdot q_1$$
$$N_{\text{inggid}} = p \cdot q_2$$

Because they share the prime $p$, we can compute:
$$p = \gcd(N_{\text{mira}}, N_{\text{inggid}})$$
$$q_2 = \frac{N_{\text{inggid}}}{p}$$

```python
import math

p = math.gcd(n_mira, n_inggid)
print(f"Shared prime bit length: {p.bit_length()}")  # 512 bits
q2 = n_inggid // p
```

With the private prime factors recovered for INGGID-HUB, we compute Euler's totient function $\phi(N_{\text{inggid}}) = (p - 1)(q_2 - 1)$ and the private exponent $d$:

$$d = e^{-1} \pmod{\phi(N_{\text{inggid}})}$$

The session key was sent from MIRA-NODE to INGGID-HUB's public key in frame `57` . Decrypting the PKCS#1 v1.5 ciphertext:

```python
c = int.from_bytes(bytes.fromhex(encrypted_session_key), "big")
d = pow(65537, -1, (p - 1) * (q2 - 1))
m = pow(c, d, n_inggid)
pt = m.to_bytes((m.bit_length() + 7) // 8, "big")
key = pt[pt.index(b"\x00", 1) + 1 :]
print("Recovered AES-128 Key:", key.hex())
```

Output:

```text
Recovered AES-128 Key: 54ec152c2eef3d9d1061228ed56c2586
```

---

## 4. Reassembling & Decrypting the Research File

We concatenate the 24 payload chunks in order ( `0001` through `0024` ) and verify the resulting SHA-256 against `ciphertext_sha256` :

```python
cipher = b"".join(chunks[f"{i:04d}"] for i in range(1, 25))
assert hashlib.sha256(cipher).hexdigest() == meta["ciphertext_sha256"]
```

We decrypt the binary using OpenSSL with AES-128-CBC:

```bash
openssl enc -d -aes-128-cbc \
  -K 54ec152c2eef3d9d1061228ed56c2586 \
  -iv d2597bb0b5d9c63a481ca46228900764 \
  -in cipher.bin -out hasil_penelitian.zip
```

The decrypted file is a valid Zip archive ( `PK\x03\x04` header). Unpacking it reveals:

```text
hasil_penelitian.zip
├── README.txt
├── dragonfly.jpg
├── experiment.log
└── sensor_data.csv
```

---

## 5. Capturing the Flag `README.txt` contains a hint:

> _"Mira, I saved the final results inside the image metadata."_

Extracting strings from `dragonfly.jpg` :

```bash
strings dragonfly.jpg | grep NEC
```

```text
NEC{r3us3d_pr1m3s_l34k_th3_r3s34rch}
```

The flag is embedded directly in the EXIF `UserComment` tag.

---

## Complete Automated Solver

```python
#!/usr/bin/env python3
import hashlib
import json
import math
import subprocess

PCAP = "penelitian.pcap"

# 1. Extract MQTT payloads from PCAP
out = subprocess.run(
    ["tshark", "-r", PCAP, "-Y", "mqtt", "-T", "fields",
     "-e", "mqtt.topic", "-e", "mqtt.msg"],
    capture_output=True, text=True
).stdout

pubs, session, meta, chunks = {}, None, None, {}
for line in out.strip().splitlines():
    parts = line.split("\t")
    if len(parts) < 2 or not parts[-1]:
        continue
    topic, msg = parts[0], parts[1]
    raw = bytes.fromhex(msg)
    if topic == "iot/crypto/public":
        j = json.loads(raw)
        pubs[j["device"]] = (int(j["n"]), int(j["e"]))
    elif topic == "iot/crypto/session":
        session = json.loads(raw)
    elif topic == "iot/transfer/meta":
        meta = json.loads(raw)
    elif topic.startswith("iot/transfer/data/"):
        chunks[topic.split("/")[-1]] = raw

n_mira, e = pubs["MIRA-NODE"]
n_ingg, _ = pubs["INGGID-HUB"]

# 2. Shared-prime GCD attack
p = math.gcd(n_mira, n_ingg)
q = n_ingg // p
assert p * q == n_ingg

# 3. Decrypt AES session key
c = int.from_bytes(bytes.fromhex(session["encrypted_session_key"]), "big")
d = pow(e, -1, (p - 1) * (q - 1))
m = pow(c, d, n_ingg)
pt = m.to_bytes((m.bit_length() + 7) // 8, "big")
key = pt[pt.index(b"\x00", 1) + 1 :]
print("[+] AES key:", key.hex())

# 4. Reassemble chunks & verify hash
cipher = b"".join(chunks[f"{i:04d}"] for i in range(1, meta["chunks"] + 1))
assert hashlib.sha256(cipher).hexdigest() == meta["ciphertext_sha256"]
open("/tmp/cipher.bin", "wb").write(cipher)

# 5. Decrypt archive & extract flag
subprocess.run(
    ["openssl", "enc", "-d", "-aes-128-cbc",
     "-K", key.hex(), "-iv", meta["iv"],
     "-in", "/tmp/cipher.bin", "-out", "/tmp/hasil.zip"],
    check=True
)
subprocess.run(["unzip", "-o", "/tmp/hasil.zip", "-d", "/tmp/hasil_out"], check=True)

jpg = open("/tmp/hasil_out/dragonfly.jpg", "rb").read()
idx = jpg.find(b"NEC{")
flag = jpg[idx : jpg.find(b"}", idx) + 1].decode()
print("[+] Flag   :", flag)
```

---

## Security Lessons

- **Never reuse RSA primes across keys:** A single greatest common divisor ($\gcd$) computation against public moduli breaks the private key of every affected entity. This vulnerability has been widely studied in embedded systems (e.g., Heninger et al., *“Mining Your Ps and Qs”*).
- **Prototype Configurations in Production:** Retaining demo cryptographic templates with static seeds or shared parameters is a recurring root cause in IoT security breaches.
- **Unencrypted Transport Metadata:** Broadcasting protocol parameters (IV, chunk size, expected SHA-256 hash) in plaintext aids attackers in verifying payload integrity and decryption attempts.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/iot-security-research/  

