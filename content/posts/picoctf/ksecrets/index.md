---
title: "picoCTF – KSecrets"
date: 2026-03-10
categories:
  - Misc
tags:
  - Misc
  - picoCTF
collections:
  - picoCTF
---



{{< admonition tldr "TL;DR" >}}

**picoCTF – KSecrets** is a misc challenge from **picoCTF**. We have a Kubernetes cluster setup and the flag is stored inside the secrets. Can you retrieve it?

{{< /admonition >}}

## Description

We have a Kubernetes cluster setup and the flag is stored inside the secrets.
Can you retrieve it?

```
Kubernetes is running at:
green-hill.picoctf.net : 56742
```

A kubeconfig file is provided.

---

#Recon

Download the kubeconfig file:

```bash
wget http://green-hill.picoctf.net:65158/kubeconfig
```

Inspect the configuration:

```bash
cat kubeconfig
```

Important part:

```yaml
server: https://127.0.0.1:6443
```

The API server points to `127.0.0.1` , which means `kubectl` will attempt to connect to the local machine instead of the remote cluster.

However, the challenge tells us the cluster is available at:

```
green-hill.picoctf.net:56742
```

Therefore we need to modify the kubeconfig.

---

# Fixing kubeconfig

Edit the kubeconfig file:

```bash
nano kubeconfig
```

Changes:

```yaml
server: https://127.0.0.1:6443
```

To:

```yaml
server: https://green-hill.picoctf.net:56742
```

Then export the configuration:

```bash
export KUBECONFIG=$(pwd)/kubeconfig
```

Since the challenge hints say to ignore TLS, we will use:

```
--insecure-skip-tls-verify
```

---

# Enumeration

First check if the cluster is accessible:

```bash
kubectl get pods --insecure-skip-tls-verify
```

Output:

```
No resources found in default namespace.
```

List all namespaces:

```bash
kubectl get ns --insecure-skip-tls-verify
```

Output:

```
NAME              STATUS
default           Active
kube-node-lease   Active
kube-public       Active
kube-system       Active
picoctf           Active
```

The challenge namespace is **picoctf**.

---

# FindingSecrets

List secrets in the namespace:

```bash
kubectl get secrets -n picoctf --insecure-skip-tls-verify
```

Example output:

```
NAME      TYPE     DATA
flag      Opaque   1
```

Retrieve the secret:

```bash
kubectl get secret flag -n picoctf -o yaml --insecure-skip-tls-verify
```

Result:

```yaml
data:
  flag: cGljb0NURntleGFtcGxlX2ZsYWd9
```

---

# Decoding the Secret

Kubernetes secrets are stored as **base64 encoded values**.

Decode the flag:

```bash
echo cGljb0NURntleGFtcGxlX2ZsYWd9 | base64 -d
```

Output:

```
picoCTF{...}
```

---

# Automated Commands

The flag can also be retrieved in one command:

```bash
kubectl get secret flag -n picoctf \
-o jsonpath='{.data.flag}' \
--insecure-skip-tls-verify | base64 -d
```

---

# Flags

```
picoCTF{REDACTED}
```

---

# Key Takeaways

* Kubernetes **Secrets are only base64 encoded**, not encrypted by default.
* If a kubeconfig with credentials is leaked, an attacker may gain full access to the cluster.
* Always secure kubeconfig files and enable secret encryption at rest.
