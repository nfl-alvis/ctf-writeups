---
title: "Sweet_Tooth"
date: 2026-04-08
categories:
  - Web
tags:
  - Web
  - WMS
collections:
  - WMS
---


{{< admonition tldr "TL;DR" >}}

**Sweet_Tooth** is a web challenge from **WMS**. Name: Sweet Tooth Value: 0 Description: Phantom Collective's CyberSpace forum looks like a normal security community.

{{< /admonition >}}

Name: Sweet Tooth
Value: 0
Description: Phantom Collective's CyberSpace forum looks like a typical security community.

=== TECHNICAL NOTES ===

The server provides a data collection endpoint for testing purposes:

  GET /collect/<token>?key=value — save data (free token, min 4 characters)
  GET /collect/<token>/view — view stored data

Example:
  fetch('/collect/mytoken123?data=hello')
  Then open: /collect/mytoken123/view
