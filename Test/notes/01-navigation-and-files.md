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

## Create, copy, move, and remove

Run from `labs/file-operations/`. If `scratch` already contains work you want to
keep, choose another scratch directory name in these commands.

```bash
mkdir -p scratch/nested
touch scratch/empty.txt
printf 'Linux practice\n' > scratch/message.txt
cp -i scratch/message.txt scratch/message-copy.txt
mv -i scratch/message-copy.txt scratch/renamed.txt
ls -l scratch
rm -i scratch/renamed.txt
rmdir scratch/nested
```

| Command | Explanation |
| --- | --- |
| `mkdir -p scratch/nested` | Creates missing parent directories; accepts existing directories |
| `touch scratch/empty.txt` | Creates an empty file if absent; otherwise updates timestamps without clearing contents |
| `printf 'Linux practice\n' > ...` | Produces text and a newline; the shell writes it to the file, replacing existing contents |
| `cp -i source destination` | Copies a file; asks before overwriting an existing destination |
| `mv -i source destination` | Moves or renames an entry; asks before overwriting |
| `rm -i path` | Asks before removing a file; answer `y` to confirm |
| `rmdir path` | Removes an empty directory; fails if it contains entries |

`cp -r source destination` copies directories recursively. If the destination
already exists as a directory, the source is copied inside it.
`rm -r directory` removes a directory and its contents. Terminal removal normally
does not use the desktop Trash. Recursive removal is unnecessary for this exercise.
