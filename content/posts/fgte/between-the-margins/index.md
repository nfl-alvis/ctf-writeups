---
title: "Between the Margins"
date: 2026-02-22
categories:
  - Crypto
tags:
  - Crypto
  - FGTE
collections:
  - FGTE
---



{{< admonition tldr "TL;DR" >}}

**Between the Margins** is a crypto challenge from **FGTE**. Given:

{{< /admonition >}}

{{< image src="./images/challenge.png" >}}

## Description Challenge

Given:

* Series of numbers:

```
  234, 235, 1, 237, 238
  ```

* Flags formats:

```
  FGTE{WORD_WORD_...}
  ```

* Reference file: `referensi.txt` The contents of `referensi.txt` consist of four pages of text entitled:

```md
# Between the Margins

## Signal Archive — Restricted

Page 1

Nothing valuable is spoken aloud. In hostile territory, clarity is a liability. Messages survive not because they are hidden, but because they are misplaced — buried inside order, routine, and habit.

Operators were taught a simple rule: never protect the message itself. Protect the way to reach it. Pages exist for this reason. Lines exist for this reason. Positions outlive words.

If you are reading this directly, you are already late.

---

Page 2

Training did not begin with language. It began with discipline. Recruits learned to count before they learned to speak. A number could point where a sentence could not.

The book was never special. Any book would do, as long as everyone agreed where to look. Meaning was not written — it was indexed.

Do not search for secrets here. Search for coordinates.

---

Page 3

Interceptors often celebrated partial success. They decoded sounds, letters, even entire phrases. What they failed to understand was intent. A decoded message can still be useless if it lacks context.

The correct solution is always smaller than expected. When the path is right, the destination is obvious.

Signals do not guide you with answers. They guide you with direction.

---

Page 4

Final directive:

If you believe the message ends here, you have misunderstood its purpose. This archive is not the destination. It is a map.

Read carefully. Count precisely. The truth lives between the margins.
```

The document structure is divided into:

* Page 1
*Page 2
* Page 3
* Page 4

---

## Technical Conceptual Analysis

The initial narrative provides important clues:

> "It's not what is said that matters, it's where one looks."
> "The signal you found is just a reference."
> "Search for coordinates."
> "Meaning was not written — it was indexed."

Words like:

* Pages
* Lines
* Positions
* Coordinates
* Indexed

refers explicitly to the **book cipher**.

In book ciphers, numbers do not represent letters directly, but rather point to certain positions in a reference text (book/document).

---

## Identify Method

The `referensi.txt` structure divided into several pages strengthens the indication that:

* These numbers are indexes
* The document functions as a "book" in the book cipher

The given series of numbers:

```
234, 235, 1, 237, 238
```

Most likely points to a specific word or position in the reference text.

---

## Decoding Process

Using the book cipher method (for example via dcode.fr or manual parsing with indexing against reference.txt), the numbers are mapped to words in the document.

Decoding result:

{{< image src="./images/solved.png" >}}

---

## Flag arrangement

Requested flag format:

```
FGTE{WORD_WORD_...}
```

Then the decoding phrase is adjusted to this format:

---

##Flags

```
FGTE{Redacted}
```
