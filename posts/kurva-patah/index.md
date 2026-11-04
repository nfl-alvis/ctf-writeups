# Kurva Patah


> **Intercepted elliptic curve Diffie-Hellman parameters were recovered. Can you break the discrete logarithm on this flawed curve and decrypt the secret transmission?**

{{< admonition tldr "TL;DR" >}}
**Kurva Patah** is a cryptography challenge featuring a vulnerable Elliptic Curve Diffie-Hellman (ECDH) implementation. Because the curve's group order is exceptionally small ($n = 10092$), the Elliptic Curve Discrete Logarithm Problem (ECDLP) is trivial to solve via direct point multiplication brute force. Once the target's private key $d$ is recovered, the AES-128-CBC key is derived from $\text{SHA-256}(\text{str}(d))[:16]$, decrypting the flag ciphertext.
{{< /admonition >}}

## Intercepted Parameters

The challenge provides intercepted transmission parameters in `intercept.json`:

- **Weierstrass Curve:** $y^2 \equiv x^3 + x + 3 \pmod{10007}$
- **Field Modulus ($p$):** $10007$
- **Curve Coefficient ($a$):** $1$
- **Generator Point ($G$):** $(16, 2698)$
- **Group Order ($n$):** $10092$
- **Target Public Key ($Q$):** $(1121, 8336)$
- **Ciphertext & IV:** AES-CBC parameters hex-encoded

---

## Cryptographic Analysis

In ECDH key exchange, a party's public key $Q$ is computed by scalar multiplication of the generator point $G$ with the private key $d$:

$$Q = d \cdot G$$

The security of ECDH depends strictly on the hardness of the **Elliptic Curve Discrete Logarithm Problem (ECDLP)**: given points $G$ and $Q$, finding the scalar $d$ should be computationally intractable.

### The Flaw: Tiny Group Order

Here, the order of the base point $G$ is tiny:

$$n = 10092$$

Because $d \in [1, n]$, the total keyspace is only $10,092$ possible values. Instead of requiring complex Pohlig-Hellman or Baby-step Giant-step algorithms, we can simply evaluate:

$$1G, 2G, 3G, \dots, nG$$

until $kG = Q$.

---

## Step-by-Step Resolution

### 1. Brute-Forcing the Discrete Logarithm

Iterating scalar multiplications on the curve:

```python
def ec_add(P, Q, p, a):
    if P is None:
        return Q
    if Q is None:
        return P
    x1, y1 = P
    x2, y2 = Q
    if x1 == x2 and (y1 + y2) % p == 0:
        return None
    if P != Q:
        slope = ((y2 - y1) * pow((x2 - x1) % p, -1, p)) % p
    else:
        slope = ((3 * x1 * x1 + a) * pow((2 * y1) % p, -1, p)) % p
    x3 = (slope * slope - x1 - x2) % p
    y3 = (slope * (x1 - x3) - y1) % p
    return (x3, y3)

def ec_mul(k, P, p, a):
    res = None
    curr = P
    while k:
        if k & 1:
            res = ec_add(res, curr, p, a)
        curr = ec_add(curr, curr, p, a)
        k >>= 1
    return res

# Search for d
for d in range(1, order + 1):
    if ec_mul(d, G, p, a) == Q:
        print(f"Recovered private key: {d}")
        break
```

Output:

```text
Recovered private key: 1826
```

The target's private key is **`1826`**.

### 2. Key Derivation & AES-128-CBC Decryption

The AES key derivation function converts the scalar integer into an ASCII string, hashes it with SHA-256, and extracts the first 16 bytes:

```python
key = sha256(str(1826).encode()).digest()[:16]
```

Using the recovered key and the provided IV, we decrypt the ciphertext under AES-128-CBC and remove PKCS#7 padding:

```python
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes

cipher = Cipher(algorithms.AES(key), modes.CBC(iv))
decryptor = cipher.decryptor()
plaintext = decryptor.update(ciphertext) + decryptor.finalize()
```

Output:

```text
WMS{sm00th_0rd3r_p0hl1g_h3llm4n_dl0g}
```

---

## Flag

```text
WMS{sm00th_0rd3r_p0hl1g_h3llm4n_dl0g}
```

---

## Security Takeaway

The underlying symmetric encryption (AES-128) was sound, but the entire cryptosystem failed because of insecure elliptic curve parameters. Elliptic curves must always be chosen with sufficiently large prime orders (e.g., NIST P-256 or Curve25519) to prevent discrete log recovery attacks.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/kurva-patah/  

