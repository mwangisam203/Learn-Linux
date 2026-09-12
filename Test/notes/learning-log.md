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

## 2026-09-11 — Text processing, searching, and permissions

**Goal:** Create realistic practice inputs and explore executable permissions.

**Starting directory:** `/home/sam/Learn-Linux/Test`

**Observed result:** Added `practice/data.csv` with a header and four invented
employee records. Added `practice/logs/app.log` with seven invented INFO, WARNING,
and ERROR records. Changed the empty dummy `backups/Test.py` from mode `644` to
`755`, giving it execute permission for owner, group, and others.

**Explanation:** The CSV supports delimiter and field exercises with `cut` and
`sort`. The log supports filtering, regular expressions, context, counts, and
pipelines with `grep`. The mode change demonstrates how `chmod` affects access;
it does not add program contents or make an empty file useful to execute.

**Progress:** I now have practice examples for navigation, files, links, content
viewing and editing, metadata, searching, pipelines, and permission modes. The
next step is to predict command output before running it and explain each option
afterward rather than only memorizing command lines.

**Next question:** How do group membership, `umask`, and parent-directory
permissions combine to determine whether a user can access or change a file?

## 2026-09-12 — Reinforcement and review

**Goal:** Connect individual commands into repeatable problem-solving workflows.

**Observed result:** Reviewed navigation, path discovery, file identity, metadata,
text viewing and editing, content searches, pipelines, and permissions. Added
short references, practice drills, and explanations of how to verify each result.

**Explanation:** Memorizing a command is less useful than knowing its input,
output, side effects, and exit status. The same inspect–predict–run–verify cycle
works for file operations, text processing, links, and access troubleshooting.

**Next question:** Can I solve each review drill without copying the answer, then
explain why every option is present?
