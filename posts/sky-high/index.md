# 📝 Writeup – OSINT: Reverse MX (Tick Tock)



{{< admonition tldr "TL;DR" >}}

**📝 Writeup – OSINT: Reverse MX (Tick Tock)** is an oint challenge from **OSINT Combine**. It is known that there are several domains that use **the same mail server** as Lufthansa. Of these domains, one of them has the name **city in Bulgaria**. Our task is to find the **full domain**.

{{< /admonition >}}

### 📌 Question Description

It is known that there are several domains that use **the same mail server** as Lufthansa.
Of these domains, one of them has the name **city in Bulgaria**.
Our task is to find the **full domain**.

---

## 🔍 Completion Steps

### 1. MX Record Enumeration

The first step is to find the mail server used by the Lufthansa domain:

```bash
dig mx lufthansa.com
```

Results:

```
lufthansa.com. IN MX 10 mxa-005f4701.gslb.pphosted.com.
lufthansa.com. IN MX 10 mxb-005f4701.gslb.pphosted.com.
```

---

### 2. Infrastructure Identification

The `pphosted.com` domain is known to belong to Proofpoint, which provides email security services.

Meaning:
➡️ Many other domains likely use the same MX.

---

### 3. Reverse MX Lookup

Next, do a **reverse MX lookup** using one of the MX records:

```
mxa-005f4701.gslb.pphosted.com
```

Tools that can be used:

* viewdns.info
* hackertarget

From here you get a list of domains that use the same mail server.

---

### 4. Filtering Based on Clue

Question clue:
➡️ The domain contains the name of a city in Bulgaria

Some major cities of Bulgaria:

* Sofia
* Varna
* Plovdiv
* Burgas

---

### 5. Analyze Results

From the list of domains resulting from reverse MX, it was found:

```
lht-sofia.com
```

* “Sofia” is the capital of Bulgaria
* This domain is also part of the Lufthansa ecosystem (Lufthansa Technik)

---

## ✅ Flag / Answer

```
lht-sofia.com
```

---

## 💡 Insights

* The **reverse MX lookup** technique is very useful for finding relationships between domains
* Shared infrastructure (such as Proofpoint) is often used by many organizations
* OSINT clues are usually simple, but require proper filtering


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/sky-high/  

