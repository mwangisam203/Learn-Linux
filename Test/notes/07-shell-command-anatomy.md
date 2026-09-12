# 07 — Shell command anatomy

A shell command line is interpreted in stages. Understanding those stages explains
why quoting, redirection, and pipelines can change a command's behavior.

## Commands, options, and arguments

```bash
grep -n -- 'ERROR' labs/file-operations/practice/logs/app.log
```

- `grep` is the command name.
- `-n` is an option requesting line numbers.
- `--` ends option parsing.
- `'ERROR'` is the search-pattern argument.
- The final path is the file argument.

Spaces normally separate words. Quotes remove their special separating effect,
so `grep 'High memory' file` passes `High memory` as one argument.

## Expansion happens before execution

Bash expands variables, command substitutions, and filename patterns before it
runs the selected command:

```bash
topic='Linux files'
printf '%s\n' "$topic"
printf '%s\n' notes/*.md
printf '%s\n' "$(pwd)"
```

Double quotes allow `$topic` and `$(pwd)` expansion while keeping each result as
one argument. Single quotes preserve characters literally. An unquoted `*.md` is
expanded by Bash into matching path arguments.

## Redirection belongs to the shell

```bash
grep 'ERROR' labs/file-operations/practice/logs/app.log > /tmp/error-lines.txt
```

Bash opens the destination before starting `grep`. This explains why redirecting
a file onto itself can truncate it before the command reads it. A pipeline similarly
connects one process's standard output to another process's standard input.

## Builtins and external commands

```bash
type cd
type grep
command -V printf
```

`cd` must affect the current shell, so it is a builtin. `grep` is normally an
external executable. `type` or `command -V` shows how Bash resolves a name, which
can also be an alias, function, or keyword.

## Review questions

1. Why should a path containing spaces be quoted?
2. Which program handles `>` before the command starts?
3. Why is `cd` implemented as a shell builtin?
4. What does `--` protect against when a filename begins with `-`?
