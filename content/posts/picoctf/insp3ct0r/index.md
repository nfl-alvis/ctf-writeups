---
title: "Insp3ct0r"
date: 2019-03-29
categories:
  - Web
tags:
  - Web Inspection
  - Client-Side Analysis
  - HTML
  - CSS
  - JavaScript
collections:
  - picoCTF
---



> **Kishor Balan tipped us off that the following code may require inspection. Can you find the secret pieces distributed across the web application?**

{{< admonition tldr "TL;DR" >}}

**Insp3ct0r** is a classic introductory web challenge from picoCTF focusing on client-side code analysis. The application distributes the flag across three different web assets: HTML comments in the main index, CSS block comments in the stylesheet, and JavaScript comments in the client script. Inspecting all three files and concatenating their fragments reveals the full flag.

{{< /admonition >}}

## Analysis

The application landing page states that the website was built using standard web technologies:

- **HTML** (HyperText Markup Language)
- **CSS** (Cascading Style Sheets)
- **JS** (JavaScript)

The hint confirms that the flag has been split into 3 parts across these client-side resources.

---

## Step-by-Step Inspection

### Step 1: Inspecting the HTML Document

We query the index page using `curl` :

```bash
curl -s http://fickle-tempest.picoctf.net:54914/
```

Reviewing the HTML comments reveals part 1/3:

```html
<!-- Html is neat. Anyways have 1/3 of the flag: picoCTF{tru3_d3 -->
```

The document also links to an external stylesheet ( `mycss.css` ) and script ( `myjs.js` ):

```html
<link rel="stylesheet" type="text/css" href="mycss.css">
...
<script src="myjs.js"></script>
```

---

### Step 2: Inspecting the CSS Stylesheet

We retrieve `mycss.css` :

```bash
curl -s http://fickle-tempest.picoctf.net:54914/mycss.css
```

At the end of the stylesheet, we find part 2/3:

```css
/* Here's part 2/3 of the flag: t3ct1ve_0r_ju5t */
```

---

### Step 3: Inspecting the JavaScript Script

We fetch `myjs.js` :

```bash
curl -s http://fickle-tempest.picoctf.net:54914/myjs.js
```

Inside the script comments, we find part 3/3:

```javascript
/* part 3/3 of the flag: _lucky?302945a7} */
```

---

## Assembling the Flag

Concatenating the three fragments:

| Parts | Source File | Contents |
| :---: | :--- | :--- |
| **1/3** | `index.html` | `picoCTF{tru3_d3` |
| **2/3** | `mycss.css` | `t3ct1ve_0r_ju5t` |
| **3/3** | `myjs.js` | `_lucky?302945a7}` |

{.table-responsive}

```text
picoCTF{tru3_d3t3ct1ve_0r_ju5t_lucky?302945a7}
```

---

##Flags

```text
picoCTF{tru3_d3t3ct1ve_0r_ju5t_lucky?302945a7}
```

---

## Security Takeaway

Never leave sensitive operational data, API keys, internal endpoints, or developer comments in client-accessible assets. All HTML, CSS, and JavaScript files are delivered in plaintext to the user's browser and can be inspected effortlessly via browser developer tools or automated curl scripts.
