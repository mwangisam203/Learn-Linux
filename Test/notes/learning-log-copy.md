# Learning log

The numbered notes explain concepts. Use this log for your own experiments and
observations. Keep dummy exercise text in `labs/`, including `practice/docs/notes.txt`.

Copy this template for each session:

## YYYY-MM-DD — Topic

**Goal:** What am I trying to understand?

**Starting directory:** Record `pwd` so relative paths make sense later.

**Command:**

```bash
# Put the command here.
```

**Parts explained:** What does the command, each flag, and each path mean?

**Prediction:** What do I expect?

**Observed result:** Paste short output or describe the change.

**Explanation:** Why did it happen? Did it match my prediction?

**Mistake and correction:** What failed, what caused it, and what fixed it?

**Next question:** What should I try or read next?

## 2026-09-09 — Inspect moved files, copies, and links

**Goal:** Understand the difference between a path, file contents, and file identity.

**Starting directory:** `/home/sam/Learn-Linux/Test`

**Observed result:** The backup text is now in `practice/docs/`; `test_main.py`
is absent; `test_main_hard.py` remains. `practice/src/test_copy.py` has a different
inode from the remaining test file. Both are empty and each has one hard link.
`docs_link` resolves to `labs/file-operations/practice/docs`.

**Explanation:** Different paths can share an inode through hard links, while
identical contents can belong to independent files. A symbolic link stores a
path. The observed state is consistent with a copy and a hard-link deletion
exercise, but does not establish the exact command history.

**Next question:** Repeat the temporary-directory link exercise and inspect the
link count before and after deleting one name. See the
[session notes](04-copy-move-and-link-practice.md) for commands and explanations.
extra line for testing diff
