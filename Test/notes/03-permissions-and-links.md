# 03 — Permissions, inodes, and links

## Read a long listing

From the repository root:

```bash
ls -li labs/links
id
```

`ls -l` shows type/permissions, hard-link count, owner, group, size in bytes,
modification time, and name. `-i` adds the inode number at the beginning.
`id` shows your user and group identities.

In `-rw-rw-r--`, the first character indicates type: `-` is a regular file, `d` a
directory, and `l` a symbolic link. The remaining characters form three permission
groups: owner, group, and others.

| Permission | Regular file | Directory |
| --- | --- | --- |
| `r` (read) | Read contents | List entry names |
| `w` (write) | Modify contents | Create, remove, or rename entries, usually also requiring `x` |
| `x` (execute/search) | Permit execution, subject to a valid format/interpreter and other restrictions | Traverse and access entries by name |

Deleting a file mainly depends on its parent directory's permissions, not on the
file's write permission. Sticky bits, ACLs, and other controls can add restrictions;
the table describes the basic model.

Try a change on a scratch file, from the repository root:

```bash
mkdir -p labs/file-operations/scratch
touch labs/file-operations/scratch/permissions.txt
chmod 640 labs/file-operations/scratch/permissions.txt
ls -l labs/file-operations/scratch/permissions.txt
chmod u+x labs/file-operations/scratch/permissions.txt
ls -l labs/file-operations/scratch/permissions.txt
```

`chmod` changes permissions. Numerically, read is `4`, write `2`, and execute `1`;
add them for each category. `640` gives the owner read/write, the group read, and
others no permissions. `u+x` adds execute for the owner, preserving other bits.
An empty file does not become a useful program merely by adding execute permission.

## Understand ownership and groups

Every filesystem object has an owning user and group. Permissions answer what the
owner, members of the owning group, and everyone else may do. Ownership answers
which user and group fill those first two roles.

From the repository root, inspect your identity and the practice files:

```bash
id
groups
ls -l labs/file-operations/backups/Test.py
stat -c 'owner=%U(%u) group=%G(%g) mode=%a (%A) name=%n' labs/file-operations/backups/Test.py
```

`id` prints your numeric user ID, primary group ID, and supplementary groups.
`groups` prints your group names. In `ls -l`, the names after the link count are
the owner and group. GNU `stat` can show both names (`%U`, `%G`) and numeric IDs
(`%u`, `%g`). Programs ultimately use numeric IDs; names are human-readable mappings.

The current dummy `Test.py` mode is `755`, displayed as `-rwxr-xr-x`:

- The owner can read, write, and execute it.
- Members of its owning group can read and execute it.
- Other users can read and execute it.

The file is empty practice data. Making it executable demonstrates a mode change,
but it still contains no program or interpreter instruction to run.

## Read symbolic and numeric modes

For each of the owner, group, and other permission sets, add these values:

| Permission | Value |
| --- | ---: |
| Read (`r`) | 4 |
| Write (`w`) | 2 |
| Execute/search (`x`) | 1 |
| No permission (`-`) | 0 |

Common modes include:

| Mode | Symbolic form for a regular file | Meaning |
| --- | --- | --- |
| `600` | `-rw-------` | Owner can read and write |
| `640` | `-rw-r-----` | Owner read/write; group read |
| `644` | `-rw-r--r--` | Owner read/write; everyone can read |
| `700` | `-rwx------` | Owner has all three permissions |
| `750` | `-rwxr-x---` | Owner all; group read/execute |
| `755` | `-rwxr-xr-x` | Owner all; everyone can read/execute |

The leading file-type character is not part of the three octal digits. Directories
often need `x` so users can traverse them. A directory with read but no execute
permission may reveal names while preventing access to their metadata or contents.

## Practice chmod safely

Work on a copy so the tracked executable fixture remains unchanged:

```bash
mkdir -p labs/file-operations/scratch
cp labs/file-operations/backups/Test.py labs/file-operations/scratch/Test.py
chmod 640 labs/file-operations/scratch/Test.py
chmod u+x labs/file-operations/scratch/Test.py
chmod g+w,o-rwx labs/file-operations/scratch/Test.py
ls -l labs/file-operations/scratch/Test.py
```

Symbolic modes use a target (`u` owner, `g` group, `o` others, `a` all), an
operation (`+`, `-`, or `=`), and permissions. Thus `u+x` adds owner execute;
`g+w,o-rwx` adds group write and removes all permissions from others. With `=`,
you set the selected permissions exactly, for example `chmod u=rw,go=r file`.

Avoid `chmod 777` as a routine fix. It grants every user write access and often
hides the real ownership or directory-access problem. Grant only the access the
program and intended users need.

Be cautious with `chmod -R`: it applies recursively, and files and directories
usually should not all receive the same execute bits. In symbolic recursive modes,
capital `X` adds execute only to directories and to files that already have an
execute bit, which is often more appropriate than lowercase `x`.

## Change group and ownership

The basic commands are:

```bash
chgrp GROUP FILE
chown OWNER FILE
chown OWNER:GROUP FILE
```

`chgrp` changes the owning group. A regular user can generally change a file they
own to a group they belong to. `chown` changes the owning user and can also change
the group; changing ownership usually requires administrative privileges.

The uppercase placeholders above are syntax descriptions, not literal commands to
run. Before changing ownership, use `id`, `getent passwd USER`, and
`getent group GROUP` to confirm the intended accounts exist. On a personal learning
machine, practice group changes on a scratch file using one of your own groups:

```bash
cp labs/file-operations/backups/Test.py labs/file-operations/scratch/owned-file.py
chgrp "$(id -gn)" labs/file-operations/scratch/owned-file.py
ls -l labs/file-operations/scratch/owned-file.py
```

`id -gn` prints your primary group name, and `$(...)` substitutes that output.
This likely leaves ownership unchanged, but safely demonstrates the command form.
Do not recursively change ownership on system paths without understanding which
service or account must own them.

## Understand default permissions with umask

Programs request initial permissions when creating files, and the shell's `umask`
removes selected bits from that request. Display the current mask with:

```bash
umask
umask -S
```

A common mask is `0022`: from typical requested modes, it removes group and other
write permission, often producing `644` files and `755` directories. Regular files
normally start without execute permission even when the mask would allow it.
The actual result can also be affected by the creating program, default ACLs, and
filesystem settings.

Change `umask` only in a subshell for a contained experiment:

```bash
(
  umask 0077
  touch labs/file-operations/scratch/private.txt
  mkdir labs/file-operations/scratch/private-dir
  ls -ld labs/file-operations/scratch/private.txt labs/file-operations/scratch/private-dir
)
```

Parentheses start a subshell, so `umask 0077` does not change the parent shell's
mask. The expected basic modes are `600` for the file and `700` for the directory,
subject to ACLs or other filesystem rules.

## Check access and troubleshoot

When access fails, inspect the entire path rather than only the final file:

```bash
namei -l labs/file-operations/practice/logs/app.log
test -r labs/file-operations/practice/logs/app.log && echo readable
test -w labs/file-operations/practice/logs/app.log && echo writable
test -x labs/file-operations/backups/Test.py && echo executable
```

`namei -l` displays each path component and its permissions when the utility is
installed. The `test` commands check access for the current process and print only
when the condition succeeds. Access control lists, mount options, read-only
filesystems, security modules, and superuser rules can affect real access beyond
the basic mode bits.

## Understand the supplied links

These inode relationships describe this working directory. Git does not preserve
hard-link relationships in a fresh clone; see [what Git preserves](04-copy-move-and-link-practice.md)
and use the temporary exercise below to recreate the experiment.

An inode stores metadata and references to file data. A directory entry associates
a name with an inode. Inode numbers identify objects within a filesystem; matching
numbers on different filesystems do not imply the same object.

From the repository root:

```bash
cd labs/links
ls -li original.txt hard.txt soft.txt
readlink soft.txt
cat original.txt hard.txt soft.txt
```

- `original.txt` and `hard.txt` are names for the same inode. Neither name is more
  fundamental. Editing either modifies the same data.
- `soft.txt` has its own inode and stores the target path `original.txt`.
  `readlink` prints that stored path; `cat` follows it.
- A relative symlink target is resolved relative to the directory containing the
  link. Moving these three entries together kept the example working.
- Symlinks do not increase the target's hard-link count.
- Hard links cannot cross filesystem boundaries and normally cannot name
  directories. Symlinks can refer to directories, other filesystems, or missing paths.

## Repeat the deletion experiment separately

The supplied links already exist. Run this in Bash to create a separate temporary
example without overwriting them:

```bash
link_lab=$(mktemp -d)
cd "$link_lab"
printf 'original content\n' > original.txt
ln original.txt hard.txt
ln -s original.txt soft.txt
ls -li original.txt hard.txt soft.txt
printf 'changed\n' >> original.txt
cat soft.txt
cat hard.txt
rm -i original.txt
# Answer y to perform the deletion experiment.
cat hard.txt
cat soft.txt
find . -xtype l
```

`mktemp -d` creates a unique temporary directory and prints its path. `$(...)`
captures the output into the `link_lab` variable. `ln target name` creates a hard
link; `ln -s target name` creates a symbolic link.

Before deletion, both reads include `changed`. After confirming removal,
`hard.txt` still reads the data. `soft.txt` points to a missing name, so reading
it fails. GNU `find . -xtype l`, with its default behavior of not following
symlinks during traversal, identifies the broken link in this example.

Removing a name does not remove the other hard links. File data is reclaimable
once the last hard link is removed and no open file handles keep it alive.

Optional cleanup of exactly the temporary entries:

```bash
rm -i hard.txt soft.txt
cd -
rmdir "$link_lab"
unset link_lab
```

Removing a symlink removes the link itself. `rmdir` succeeds only when the temporary
directory is empty; declining a removal prompt leaves it nonempty.

## Link to a directory

Start again from the repository root. Run once; inspect `practice_link` first if
it already exists.

```bash
ln -s file-operations/practice labs/practice_link
ls -l labs/practice_link
ls labs/practice_link/
```

The target is relative to `labs/`, where the link lives. The trailing slash in the
last command accesses the target directory's contents. To remove only this link,
use `rm -i labs/practice_link` with no trailing slash.

## Check your understanding

1. Which two supplied names share an inode, and why?
2. Why can a symlink exist when its target does not?
3. Why can moving a relative symlink by itself break it?
4. How do directory permissions differ from file permissions?
5. What is the difference between changing a mode with `chmod` and changing an
   owner with `chown`?
6. Why does mode `755` not make an empty file a useful program?
7. Why is `chmod -R 777` usually a poor solution?
8. How does a subshell keep a practice `umask` from changing your main shell?
