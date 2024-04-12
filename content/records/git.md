---
title: git
date: 2024-04-11
description: "Git is a beautiful tool that really tries to hide itself by failing fantastically at explaining its functionality."
tags: -tools
---

---
A case

My local branch diverged with the remote branch.

```mermaid
gitGraph
	commit
	branch remote
	commit id: "remote 1"
	commit id: "remote 2"
	checkout main
	commit id: "main 3"
	commit id: "main 4"
```

I run `git push` it failed:

> [!fail] push failed
> because the remote contains work that you do not have locally. run `git pull` before push.

git pull noticed that I would need to merge or rebase to solve the divergent problem.

the merge senario:

```mermaid
gitGraph
	commit
	branch remote
	commit id: "remote 1"
	commit id: "remote 2"
	checkout main
	commit id: "main 3"
	commit id: "main 4"
	merge remote
```

I went with the rebase route `git rebase main/origin`

There was conflict, and I manually resolved it.
```mermaid
gitGraph
	commit id: "remote 1"
	commit id: "remote 2"
	commit id: "main 3"
	commit id: "main 4"
```

2024-04-11