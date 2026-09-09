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
