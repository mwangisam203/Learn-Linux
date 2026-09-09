# 01 — Navigation and file operations

## Understand paths first

The filesystem is a tree starting at `/`, the root directory. Your current working
directory is where relative paths begin.

| Path | Meaning |
| --- | --- |
| `/` | Filesystem root, different from the root user |
| `/home/sam` | Absolute path: starts at `/` |
| `labs/file-operations` | Relative path: starts at your current directory |
| `.` | Current directory |
| `..` | Parent directory |
| `~` | Your home directory when expanded by the shell |

Start from the repository root:

```bash
pwd
ls
ls -la
cd labs/file-operations
pwd
ls practice
```

- `pwd` prints your current working directory.
- `ls` lists entries, normally excluding names beginning with a dot.
- `ls -la` combines `-l` (long details) and `-a` (all entries, including hidden ones).
- `cd` changes the shell's working directory. `ls practice` lists that folder
  without moving you into it.
- `cd ..` goes up one level; `cd` with no argument goes home; `cd -` returns to
  your previous working directory.

Typical Linux filesystems distinguish uppercase and lowercase: `Test.py` and
`test.py` are different names. Quote paths with spaces: `ls "my folder"`.
A leading dot hides a name from ordinary listings; it does not restrict access.
