# 06 — Viewing and editing file content

Linux offers several ways to inspect and change text. Choose a command based on
the task: print a small file, page through a long file, edit interactively, or
apply a predictable change from the command line.

All examples use made-up practice data. Start from the repository root:

```bash
cd labs/file-operations
mkdir -p scratch
printf 'server=development\nport=8080\ndebug=true\n' > scratch/settings.conf
```

Repeating the final command replaces `scratch/settings.conf` with the original
three-line sample.

## Identify a file before opening it

````bash
file scratch/settings.conf
stat -c 'type=%F bytes=%s permissions=%A name=%n' scratch/settings.conf

##testing edits via terminal```

`file` examines content and reports its likely type. `stat` reports metadata rather
than contents. This is useful when a filename or extension does not tell you what
the file contains. Avoid printing unknown binary files directly because they can
send control characters to the terminal.

## Choose a viewing command

| Command | Best use | Changes the file? |
| --- | --- | --- |
| `cat file` | Print a short file completely | No |
| `less file` | Read or search a long file one screen at a time | No |
| `head -n 10 file` | Show the first 10 lines | No |
| `tail -n 10 file` | Show the last 10 lines | No |
| `tail -f file` | Continue watching lines appended to a log | No |
| `nl -ba file` | Display all lines with numbers, including blank lines | No |
| `grep -n 'text' file` | Display matching lines with line numbers | No |

Try them on the sample:

```bash
cat scratch/settings.conf
nl -ba scratch/settings.conf
head -n 2 scratch/settings.conf
tail -n 1 scratch/settings.conf
grep -n 'port' scratch/settings.conf
less scratch/settings.conf
````

Inside `less`, use the arrow keys or Page Up/Page Down to move, `/text` to search
forward, `?text` to search backward, `n` for the next match, `N` for the previous
match, `g` for the beginning, `G` for the end, and `q` to quit. `less` may show
short input without filling the screen; it is still active until you press `q`.

Stop `tail -f` with `Ctrl+C`. That key combination interrupts the running command;
it does not copy text as it commonly does in graphical applications.

## Edit interactively with nano

`nano` is approachable for small manual edits. It may not be installed on every
system, so check first:

```bash
command -v nano
nano scratch/settings.conf
```

`command -v nano` prints the executable path when Bash can find it. A blank result
with a nonzero exit status means it is unavailable.

In `nano`, the `^` shown in shortcuts means Ctrl:

| Shortcut | Action                                                             |
| -------- | ------------------------------------------------------------------ |
| `Ctrl+O` | Write the current buffer to a file; press Enter to accept the name |
| `Ctrl+X` | Exit; nano asks about unsaved changes                              |
| `Ctrl+W` | Search for text                                                    |
| `Ctrl+K` | Cut the current line                                               |
| `Ctrl+U` | Paste the most recently cut text                                   |
| `Alt+U`  | Undo                                                               |
| `Alt+E`  | Redo                                                               |

Saving writes the editor buffer to disk. Exiting without saving discards the
buffer changes. Read nano's prompt carefully before confirming a filename.

## Edit interactively with Vim

Vim is powerful and commonly available, but it uses modes. Check for it and open
the practice file with:

```bash
command -v vim
vim scratch/settings.conf
```

Vim begins in Normal mode. These basics are enough for a first edit:

| Keys               | Action                               |
| ------------------ | ------------------------------------ |
| `i`                | Enter Insert mode before the cursor  |
| `Esc`              | Return to Normal mode                |
| `/text` then Enter | Search forward in Normal mode        |
| `n`                | Go to the next search match          |
| `u`                | Undo in Normal mode                  |
| `:w` then Enter    | Save                                 |
| `:q` then Enter    | Quit if there are no unsaved changes |
| `:wq` then Enter   | Save and quit                        |
| `:q!` then Enter   | Quit and discard unsaved changes     |

If typing appears to run commands instead of inserting text, press `i`. If you
cannot exit Insert mode, press `Esc`, then enter the appropriate colon command.
Use `vimtutor` when installed for a guided lesson.

## Replace or append content from the shell

Redirection is performed by the shell before it runs the command:

```bash
printf 'server=testing\n' > scratch/settings.conf
printf 'port=9090\n' >> scratch/settings.conf
cat scratch/settings.conf
```

`>` creates the destination or truncates it before writing. `>>` creates it or
appends at the end. Neither asks before changing an existing file. Check the path
carefully, especially when using `>`.

To write several lines clearly, use a here-document:

```bash
cat > scratch/message.txt <<'EOF'
This is practice text.
Variables such as $USER remain literal in this quoted here-document.
EOF
cat scratch/message.txt
```

The shell supplies every line through the line containing only `EOF` as standard
input to `cat`. Quoting the first `'EOF'` prevents parameter, command, and arithmetic
expansion inside the text. The final delimiter must be alone on its line.

## Write through a pipeline with tee

`tee` copies standard input both to standard output and to one or more files:

```bash
printf 'mode=practice\n' | tee scratch/mode.conf
printf 'enabled=yes\n' | tee -a scratch/mode.conf
cat scratch/mode.conf
```

Without `-a`, `tee` replaces the destination. With `-a`, it appends. `tee` is
especially helpful when you want to see what is being saved or when a pipeline
must write through `sudo`, because the shell handles `>` before `sudo` runs:

```bash
printf '%s\n' 'new text' | sudo tee /path/to/protected-file
```

That protected-path command is only a syntax example. Do not run it without a
specific system file you intend and are authorized to change. Add `> /dev/null`
after `tee` if you deliberately want to hide its normal screen copy.

## Make targeted noninteractive edits with sed

`sed` transforms a text stream. Start by previewing the output:

```bash
printf 'server=development\nport=8080\ndebug=true\n' > scratch/settings.conf
sed 's/^port=.*/port=9090/' scratch/settings.conf
cat scratch/settings.conf
```

The `s/old/new/` command substitutes text. Here, `^port=` matches `port=` only at
the beginning of a line, and `.*` matches the rest of that line. Plain `sed`
prints transformed output but leaves the source unchanged, as the following `cat`
demonstrates.

After verifying the preview, GNU `sed -i.bak` edits the file and keeps its previous
contents in a backup:

```bash
sed -i.bak 's/^port=.*/port=9090/' scratch/settings.conf
cat scratch/settings.conf
cat scratch/settings.conf.bak
```

`-i.bak` means edit in place and use `.bak` as the backup suffix. Behavior and
syntax for `-i` vary across Unix systems, so consult `sed --help` or `man sed` on
the system you are using. Avoid bare `sed -i` until you have tested the expression
and decided that you do not need a backup.

## Verify every edit

After editing, inspect the result and the Git change when applicable:

```bash
cat -n scratch/settings.conf
grep -n '^port=' scratch/settings.conf
stat -c 'bytes=%s modified=%y name=%n' scratch/settings.conf
git diff -- scratch/settings.conf
```

`git diff` only shows changes to files tracked by Git. A scratch file may produce
no diff because it is untracked or ignored; `git status --short` tells you which.
For configuration files or scripts, also run the program's validation command if
one exists. Seeing the desired text does not prove that its syntax is valid.

## Permissions and safer habits

If an editor reports “Permission denied,” inspect the file and its parent directory:

```bash
ls -l scratch/settings.conf
ls -ld scratch
```

Do not solve every permission error with `sudo`. Confirm that you chose the right
file and determine who owns it first. For an important file, create a backup that
preserves its mode and timestamps before editing:

```bash
cp -p important.conf important.conf.bak
```

That command is a general example; it assumes `important.conf` exists. A backup in
the same directory is convenient but does not protect against disk loss. Also note
that editors and in-place tools may replace a file internally, which can affect
symlinks, hard links, permissions, or extended metadata depending on the program.
Inspect links with `stat`, `readlink`, and `ls -li` before editing through them.

## Check your understanding

1. Which command would you choose for a large log, and how do you leave it?
2. What is the difference between `>` and `>>`?
3. Why should you preview a `sed` substitution before using `-i`?
4. How do `nano` and Vim indicate or handle unsaved changes?
5. Why can `git diff` show nothing after you edit a scratch file?
6. What should you inspect before editing a symbolic or hard-linked path?
