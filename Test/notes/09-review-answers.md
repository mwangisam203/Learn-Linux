# 09 — Review drill answers

These are example solutions for [the review drills](08-review-drills.md). Equivalent
commands can also be correct. Run modifying commands only on scratch data.

```bash
# 1
pwd
cd labs/file-operations
cd -

# 2
find labs -type f -iname '*.py'

# 3
readlink labs/links/soft.txt
realpath labs/links/soft.txt

# 4
head -n 3 labs/file-operations/practice/logs/app.log | nl -ba

# 5
grep -c 'ERROR' labs/file-operations/practice/logs/app.log

# 6
cut -d, -f1,3 labs/file-operations/practice/data.csv

# 7
cut -d' ' -f3 labs/file-operations/practice/logs/app.log | sort | uniq -c

# 8
grep -n -C 1 'WARNING' labs/file-operations/practice/logs/app.log

# 9
stat -c 'device=%d inode=%i links=%h name=%n' labs/links/original.txt labs/links/hard.txt

# 10
stat labs/links/soft.txt
stat -L labs/links/soft.txt

# 12
mkdir -p labs/file-operations/scratch
touch labs/file-operations/scratch/mode-practice.txt
chmod 640 labs/file-operations/scratch/mode-practice.txt

# 14
printf 'one\ntwo\nthree\n' > labs/file-operations/scratch/three-lines.txt

# 15
sed 's/two/second/' labs/file-operations/scratch/three-lines.txt
sed -i.bak 's/two/second/' labs/file-operations/scratch/three-lines.txt

# 16
cat -n labs/file-operations/scratch/three-lines.txt
stat labs/file-operations/scratch/three-lines.txt
git status --short
git diff -- labs/file-operations/scratch/three-lines.txt
```

For drill 11, `750` is `rwxr-x---`: owner gets `7` (`4+2+1`), group gets `5`
(`4+1`), and others get `0`. For drill 13, removing a name changes the directory
entry, so write and execute/search permissions on the parent directory are central.
Sticky bits, ACLs, and other controls can impose additional rules.

For drill 16, `git diff` is empty for an untracked file; consult `git status` first.
Verification should match the kind of change: read content, inspect metadata, and
run a syntax checker or application validation command when one exists.
