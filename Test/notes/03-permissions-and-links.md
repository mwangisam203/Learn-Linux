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

## Understand the supplied links

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
