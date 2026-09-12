# Command reference

This page is a reminder after studying the detailed notes. Use `--help`, `man`,
or the numbered lesson when you need exact behavior.

| Goal | Command example |
| --- | --- |
| Print current directory | `pwd` |
| List hidden entries with details | `ls -la` |
| Change directory | `cd PATH` |
| Resolve a path | `realpath PATH` |
| Find regular files by name | `find START -type f -name 'PATTERN'` |
| Identify content type | `file PATH` |
| Show metadata | `stat PATH` |
| Print a small text file | `cat FILE` |
| Page through text | `less FILE` |
| Show beginning or end | `head -n N FILE`, `tail -n N FILE` |
| Search literal content | `grep -Fn 'TEXT' FILE` |
| Search recursively | `grep -rn 'PATTERN' DIRECTORY` |
| Select delimited fields | `cut -d, -f1,3 FILE` |
| Sort and count values | `sort FILE \| uniq -c` |
| Count lines, words, bytes | `wc -l -w -c FILE` |
| Copy or move safely | `cp -i SOURCE DEST`, `mv -i SOURCE DEST` |
| Make a directory | `mkdir -p PATH` |
| Change permissions | `chmod MODE PATH` |
| Change owning group | `chgrp GROUP PATH` |
| Inspect identity and groups | `id`, `groups` |
| Create hard or symbolic link | `ln TARGET NAME`, `ln -s TARGET NAME` |
| Show symlink target | `readlink LINK` |
| Review Git state | `git status --short`, `git diff` |

Place options before paths where practical, quote data containing spaces or shell
characters, and use `--` before unusual path arguments that begin with `-`.
