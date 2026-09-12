# Practice labs

Everything below this directory is made-up practice material. Filenames resembling
applications, backups, configurations, logs, or employee records do not represent
a real system.

- `file-operations/` supports navigation, copying, text processing, and permissions.
- `links/` supports inode, hard-link, and symbolic-link experiments.

Before modifying a path, run `pwd`, `ls -ld PATH`, and when useful `stat PATH`.
Prefer creating a `scratch/` directory for repeatable exercises. Git tracks some
fixtures so `git diff` can demonstrate changes; inspect status before deciding
whether to keep or restore an exercise result.

Commands such as `rm`, `chmod`, and redirection operate on any path supplied to
them. Being inside a practice repository does not make an incorrect absolute path
safe, so read each command and resolve its target before pressing Enter.
