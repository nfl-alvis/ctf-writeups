---
title: "Git Is Good"
date: 2026-02-22
categories:
  - Forensics
tags:
  - Git Forensics
  - Version Control
  - Commit History
  - Git Show
collections:
  - CTFlearn
---



> **The flag used to be there. But then I redacted it. Good Luck.**

{{< admonition tldr "TL;DR" >}}

**Git Is Good** is a version control forensics challenge. After extracting the provided archive, we discover a `.git` repository folder. While the working tree file `flag.txt` only contains a decoy `___FL0X___` , inspecting the commit history using `git log` and diffing previous revisions with `git show` reveals the original plaintext flag before it was overwritten.

{{< /admonition >}}

## Initial Analysis

We are given a ZIP archive named `gitIsGood.zip` . Extracting the archive produces the following directory structure:

```text
gitIsGood/
├── .git/
└── flag.txt
```

Reading the contents of `flag.txt` :

```bash
cat flag.txt
```

Output:

```text
flag{REDACTED}
```

The file contains only a redacted placeholder. However, the presence of the `.git/` folder indicates that the full repository history and metadata have been preserved.

---

## Investigating Git Commit History

In Git, modifying or deleting a file in a new commit does not remove the data from the repository's history unless the object database is purged with a history rewrite (such as `git filter-repo` ).

We review the commit history with `git log` :

```bash
git log --oneline
```

Output:

```text
d10f77c (HEAD -> master) Edited files
195dd65 Edited files
6e824db edited files
```

Three successive commits modified the project files.

---

## Analyzing Commit Diffs

To see what changes occurred in the most recent commit ( `d10f77c` ), we inspect its patch diff using `git show` :

```bash
git show d10f77c4e766705ab36c7f31dc47b0c5056666bb
```

Output:

```diff
commit d10f77c4e766705ab36c7f31dc47b0c5056666bb (HEAD -> master)
Author: LaScalaLuke <lascala.luke@gmail.com>
Date:   Sun Oct 30 14:33:18 2016 -0400

    Edited files

diff --git a/flag.txt b/flag.txt
index 8684e68..c5250d0 100644
--- a/flag.txt
+++ b/flag.txt
@@ -1 +1 @@
-flag{protect_your_git}
+flag{REDACTED}
```

The diff clearly shows the removed line prefixed with `-` :

```text
-flag{protect_your_git}
```

The flag was simply modified in the latest commit, leaving the original secret completely preserved in Git's historical tree objects.

---

##Flags

```text
flag{protect_your_git}
```

---

## Key Takeaways

1. **Git Retains Full History:** Removing a password, API token, or secret in a subsequent commit does not secure it. Anyone with access to the repository clone can checkout or diff previous commits.
2. **Proper Secret Revocation & Purging:** When a credential is accidentally committed to version control:
   - Immediately revoke and rotate the secret.
   - Use specialized tools like `git-filter-repo` or BFG Repo-Cleaner to permanently scrub objects from the Git database if repository scrubbing is required.
   - Add sensitive file patterns to `.gitignore` to prevent inadvertent tracking.
