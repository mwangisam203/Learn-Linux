# 04 — Copy, move, and link practice

## What changed in this session

These are empty, made-up practice files. The table records the observed filesystem
state, not a claim about the exact commands previously typed.

| Before | Now | Observation |
| --- | --- | --- |
| `labs/file-operations/backups/file_backup.txt` | `labs/file-operations/practice/docs/file_backup.txt` | The file has moved into the mock docs directory |
| `labs/file-operations/test_main.py` | `labs/file-operations/test_main_hard.py` | The old name is absent; the remaining name has the same inode recorded for the old file earlier in this workspace |
| No `test_copy.py` | `labs/file-operations/practice/src/test_copy.py` | An empty regular file with a different inode from `test_main_hard.py` |
| No `docs_link` | `docs_link -> labs/file-operations/practice/docs` | A relative symbolic link to the mock docs directory |

Git initially displayed six changed paths: two deletions and four untracked
entries. A move can appear as a deletion plus an untracked destination before
staging. Git detects renames by comparing content; it does not record the shell
command used. Because these files are empty, content alone cannot establish which
file was copied or moved from which source.

## Inspect the result without changing it

For a full explanation of output fields, timestamps, and symlink handling, see
[file metadata with stat](05-file-metadata-with-stat.md).

Run from the repository root (`Test/`):

```bash
pwd
ls -li labs/file-operations/test_main_hard.py
ls -li labs/file-operations/practice/src/test_copy.py
stat -c 'device=%d inode=%i links=%h bytes=%s name=%n' labs/file-operations/test_main_hard.py labs/file-operations/practice/src/test_copy.py
readlink docs_link
ls -ld docs_link
ls docs_link/
```

- `ls -i` shows inode numbers; `-l` adds permissions, hard-link count, and metadata.
- GNU `stat -c` prints a chosen format: `%d` is the device number, `%i` the inode,
  `%h` the hard-link count, `%s` the size in bytes, and `%n` the name. Compare both
  device and inode when checking whether two names refer to the same file.
- `readlink docs_link` prints the stored target path without listing its contents.
- `ls -ld docs_link` displays the link entry itself; `-d` requests the directory
  entry rather than listing a directory's contents.
- `ls docs_link/` follows the link and lists the target directory.

At inspection, `test_main_hard.py` and `test_copy.py` each had a hard-link count
of one and different inodes. Equal contents do not imply shared file identity.
The word `hard` in a filename does not itself create or prove a hard link.

## Move versus copy

These commands illustrate how the paths could be produced from the **old layout**.
Do not rerun them against the current lab: the old source names are already absent.

```bash
mv -i labs/file-operations/backups/file_backup.txt labs/file-operations/practice/docs/file_backup.txt
cp -i labs/file-operations/test_main.py labs/file-operations/practice/src/test_copy.py
```

`mv source destination` moves or renames a name. On the same filesystem, a regular
file normally retains its inode. Across filesystems, `mv` must copy the data and
remove the source after a successful copy, so inode identity is not preserved.

`cp source destination` leaves the source present and, when the destination does
not exist, creates a separate file. Subsequent edits to that new copy do not edit
the source. `-i` asks before overwriting an existing destination for both commands.

## Why the remaining hard-link name can still work

A possible sequence from the old layout is:

```bash
ln labs/file-operations/test_main.py labs/file-operations/test_main_hard.py
rm -i labs/file-operations/test_main.py
```

`ln` without `-s` adds another name for the same inode. While both names exist,
they share contents and the inode's hard-link count increases. After confirming
`rm`, the original name disappears, but the other name still accesses the data.
The count drops back to one if these were the only two hard links.

A simple rename could also produce the final name and inode seen here. Final
state alone cannot prove the exact command sequence. The important lesson is that
removing one hard-link name does not invalidate another name for the same inode.
Use the [isolated experiment](03-permissions-and-links.md) to observe every step.

## How the directory symlink resolves

The existing link is equivalent to creating this from the repository root:

```bash
ln -s labs/file-operations/practice/docs docs_link
```

This is a creation example, not a command to repeat while `docs_link` exists.
`ln -s target link_name` creates a symbolic link. Here, the target is relative to
the directory containing `docs_link`, so it resolves into this repository's lab.
The link does not duplicate the directory. Editing a file through `docs_link/`
edits the file in the actual docs directory.

Moving the link by itself can break its relative target. Removing only the link
with `rm -i docs_link` leaves the target directory intact. Use no trailing slash
when removing the link itself.

## What Git preserves

Git tracks file content, paths, a limited set of modes, and symbolic-link targets.
It does **not** preserve inode numbers or hard-link relationships. After a fresh
clone, tracked hard-linked regular files are normally separate files, even if they
shared an inode in the original working directory. Recreate hard links with `ln`
when repeating that experiment. On systems supporting symlinks with Git configured
to use them, Git recreates the symlink from its stored target string.

## Check your understanding

1. Why can six Git status entries represent fewer than six filesystem operations?
2. Why do two empty files with different inodes remain independent files?
3. Why does a remaining hard-link name have a count of one after the other is removed?
4. What directory is the base for resolving `docs_link`'s relative target?
5. Why should you recheck inode relationships after cloning?
