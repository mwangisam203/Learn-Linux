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

## Inspect the practice tree

Still in `labs/file-operations/`:

```bash
find practice -maxdepth 2 -print
ls -la practice
file practice/config.json
wc -c practice/config.json
```

`find` walks a directory tree. `-maxdepth 2` limits descent, counting the starting
path as depth zero; `-print` prints each selected path. `file` identifies content
type, and `wc -c` counts bytes. The supplied JSON file is empty: its extension is
only a name, and it does not contain valid JSON yet.


When in doubt, use find to locate it, then cat the result

If you're not sure where a file actually lives:

bash
#example -->> find ~ -name "01-navigation-and-files.md"


cd ~/Learn-Linux/Test
realpath notes/01-navigation-and-files.md        # absolute path from a relative one
realpath labs/links/soft.txt                      # follows the symlink to original.txt's real location
readlink labs/links/soft.txt                       # shows the raw, unresolved target
ls -la labs/links/                                  # visually confirms the -> target

## Check your understanding

1. Why does `ls practice` leave `pwd` unchanged?
2. Can you locate `.env` using `ls -la practice`?
3. Rename your scratch message and locate it without changing directories.
4. Explain the difference between `rmdir` and `rm` in your learning log.

Round 5 — locate gotcha, same idea

Right after you create test_copy.py in step 1 above, before touching updatedb:

bash
locate test_copy.py     # very likely empty — index doesn't know it exists yet
sudo updatedb
locate test_copy.py     # now it should show the full path
