# 05 — File metadata with stat

## Purpose and basic syntax

`stat` displays metadata: information about a file, such as its size, ownership,
permissions, inode, link count, and timestamps. `cat` displays the file's contents.
An empty practice file still has metadata even though `cat` prints nothing.

These examples use GNU `stat` on Linux. Start in this repository's root (`Test/`):

```bash
stat labs/file-operations/test_main_hard.py
stat labs/file-operations/test_main_hard.py labs/file-operations/practice/src/test_copy.py
```

The syntax is `stat [options] path...`: you can inspect several paths in one command.
`stat` does not change the file's contents. Exact values depend on your filesystem
and what you have done to the file, so learn the fields rather than memorizing output.

## Understand the default output

| Field | Meaning |
| --- | --- |
| `File` | The path being inspected |
| `Size` | Logical size in bytes; an empty regular file has size zero |
| `Blocks` | Allocated storage, reported in units of 512 bytes on GNU/Linux; this can differ from logical size |
| `IO Block` | Preferred size for efficient I/O, not the file's size |
| File type | Regular file, directory, symbolic link, or another type |
| `Device` and `Inode` | Together identify the file object on the filesystem |
| `Links` | Number of hard-link names referring to the inode |
| `Access` with a mode such as `0640/-rw-r-----` | Numeric and symbolic permissions |
| `Uid` and `Gid` | Owner and group IDs, usually accompanied by names |
| `Access` with a date | Last access time (`atime`) |
| `Modify` | Last data modification time (`mtime`) |
| `Change` | Last inode status change time (`ctime`), not creation time |
| `Birth` | Creation time if available; `-` means unknown or unsupported |

For a directory, `Size` is not the sum of the sizes of its contents. For a symbolic
link inspected without following it, the size describes the stored target path.
A sparse file can have a large logical size while using relatively few blocks.

## Distinguish the timestamps

- Reading contents can update **atime**, but filesystem policies such as `relatime`
  and `noatime` mean it need not change after every read.
- Writing contents normally updates **mtime** and **ctime**.
- Changing permissions with `chmod` updates **ctime**, even if contents are unchanged.
- **Birth** is separate from ctime. Do not use `Change` as a creation date.

Use an isolated temporary directory to observe these changes in Bash:

```bash
stat_lab=$(mktemp -d)
printf 'first line\n' > "$stat_lab/example.txt"
stat "$stat_lab/example.txt"
chmod 600 "$stat_lab/example.txt"
stat "$stat_lab/example.txt"
printf 'second line\n' >> "$stat_lab/example.txt"
stat "$stat_lab/example.txt"
```

Compare permissions after `chmod`, then size and modification time after appending.
Run each command separately; very quick operations can share timestamps at the
filesystem's recorded resolution. `mktemp -d` creates a unique directory, and
`$(...)` captures its path in the variable. Optional cleanup:

```bash
rm -i "$stat_lab/example.txt"
rmdir "$stat_lab"
unset stat_lab
```

Answer `y` to remove the sample. `rmdir` only removes an empty directory.

## Select fields with -c

From the repository root:

```bash
stat -c 'name=%n type=%F bytes=%s mode=%a (%A) owner=%U:%G' labs/file-operations/test_main_hard.py
stat -c 'device=%d inode=%i links=%h name=%n' labs/links/original.txt labs/links/hard.txt
stat -c 'access=%x modify=%y change=%z birth=%w' labs/file-operations/test_main_hard.py
```

`-c` (or `--format`) accepts a format string and adds a newline for each file.
Quotes keep the format, including its spaces, together as one shell argument.

| Placeholder | Value |
| --- | --- |
| `%n`, `%F` | Filename and file type |
| `%s` | Logical size in bytes |
| `%a`, `%A` | Octal permissions; symbolic permissions with file type |
| `%U`, `%G` | Owner and group names |
| `%d`, `%i`, `%h` | Device number, inode number, hard-link count |
| `%x`, `%y`, `%z`, `%w` | Access, modification, status-change, and birth timestamps |
| `%b`, `%B` | Allocated block count and bytes per reported block; multiply for allocated bytes |

Matching device and inode values establish that two names refer to the same object
at inspection time. Matching contents alone do not. Git does not preserve hard-link
relationships after cloning; use the [link exercise](03-permissions-and-links.md)
to create them if needed.

For explicit newlines inside the format, use `--printf`, which interprets escapes
and does not add its own final newline:

```bash
stat --printf='File: %n\nBytes: %s\n' labs/file-operations/test_main_hard.py
```

## Inspect a symlink versus its target

From the repository root, without adding a trailing slash to `docs_link`:

```bash
stat docs_link
stat -L docs_link
readlink docs_link
```

By default, `stat` reports the symbolic link itself. `-L` (`--dereference`) follows
it and reports the target directory. `readlink` prints the stored target string.
For a broken symlink, plain `stat` can still inspect the link, but `stat -L` fails
because it cannot resolve the target.

## File status versus filesystem status

```bash
stat -f .
```

`-f` (`--file-system`) reports the filesystem containing the path: its type, blocks,
and inode capacity. It does not list files or recursively measure a directory.
Format placeholders have different meanings with `-f`; consult `stat --help`
before reusing a file format string for filesystem output.

For local reference, use `stat --help`, `man stat`, or
`info '(coreutils) stat invocation'` where manuals are installed.

## Check your understanding

1. Why can `cat` print nothing while `stat` shows many fields for the same file?
2. Which timestamp changes when permissions change? Is it the creation time?
3. Why compare both device and inode when checking for hard links?
4. How does `stat docs_link` differ from `stat -L docs_link`?
