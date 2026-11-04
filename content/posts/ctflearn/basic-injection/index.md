---
title: "Basic Injection"
date: 2026-02-22
categories:
  - Web
tags:
  - SQL Injection
  - Authentication Bypass
  - Tautology
collections:
  - CTFlearn
---

> **See if you can leak the whole database using what you know about SQL Injections.**

{{< admonition tldr "TL;DR" >}}
**Basic Injection** is a fundamental web exploitation challenge demonstrating classic SQL Injection. The backend query incorporates user input directly via string concatenation without parameterization. Submitting a tautology payload (`' OR 1=1 -- -`) forces the SQL statement to evaluate to true for every record in the table, dumping the entire database contents and revealing the flag.
{{< /admonition >}}

## Initial Analysis

Opening the target application presents a simple query form that takes a user's name:

{{< image src="./images/chall.webp" >}}

The interface discloses the underlying SQL statement structure:

```sql
SELECT * FROM webfour.webfour WHERE name = '$input'
```

### Vulnerability Identification

The core flaw is that `$input` is directly interpolated into the SQL command string without using **prepared statements** or input sanitization. This allows an attacker to break out of the string literal delimiter (`'`) and inject arbitrary SQL syntax.

---

## Exploitation

We construct a classic tautological SQL injection payload:

```sql
' OR 1=1 -- -
```

### Payload Breakdown

- `'` terminates the initial string literal for the `name` column.
- `OR 1=1` introduces a boolean condition that always evaluates to **TRUE**.
- `-- -` comments out the trailing single quote and the remainder of the original query.

When the database engine processes the concatenated query:

```sql
SELECT * FROM webfour.webfour WHERE name = '' OR 1=1 -- -';
```

Because the `WHERE` condition resolves to `TRUE` for every row, the database bypasses filtering and outputs all records stored within `webfour.webfour`.

{{< image src="./images/solved.webp" >}}

Submitting the payload dumps the complete table, exposing the flag.

---

## Flag

```text
CTFlearn{th4t_is_why_you_n33d_to_sanitiz3_inputs}
```

---

## Remediation

To prevent SQL Injection vulnerabilities, developers should:

1. **Use Parameterized Queries (Prepared Statements):**
   ```php
   $stmt = $pdo->prepare('SELECT * FROM webfour.webfour WHERE name = :name');
   $stmt->execute(['name' => $input]);
   $results = $stmt->fetchAll();
   ```
2. **Apply Principle of Least Privilege:** Restrict database user accounts so they only have access to specific schemas and tables required for standard operations.
