# 02 — Reading, searching, and shell input/output

## Create sample text

Start at the repository root:

```bash
cd labs/file-operations
mkdir -p scratch
printf 'INFO starting\nERROR missing file\nINFO finished\n' > scratch/events.log
```

This is invented log data. Repeating `printf` replaces this sample file.

## Read and count

```bash
cat scratch/events.log
head -n 2 scratch/events.log
tail -n 1 scratch/events.log
wc -l scratch/events.log
less scratch/events.log
```

- `cat` writes the whole file to standard output, usually the terminal. Multiple
  filenames make it write their contents in order.
- `head -n 2` shows the first two lines; `tail -n 1` shows the last line.
- `wc -l` counts newline characters: three in this sample. A final line without
  a newline can make this differ from the number of lines you visually count.
- `less` opens a scrollable view. Press `/`, type a search, and press Enter;
  `n` goes to the next match and `q` quits. Use `cat` if `less` is unavailable.
- `tail -f file` watches for appended data. Stop it with `Ctrl+C`.

## Search contents versus names

Still in `labs/file-operations/`:

```bash
grep -n 'ERROR' scratch/events.log
grep -i 'info' scratch/events.log
grep -F 'missing file' scratch/events.log
find practice -type f -name '*.py'
find practice -type d
```

`grep` searches **file contents** and prints matching lines. `-n` adds line numbers,
`-i` ignores letter case, and `-F` treats the pattern as literal text instead of a
regular expression. The first command prints `2:ERROR missing file`.

`find` searches **filesystem entries**. `-type f` selects regular files, `-type d`
selects directories, and `-name '*.py'` matches names ending in `.py`. Quotes keep
Bash from expanding `*` before `find` receives it. Empty Python files can match a
filename search while containing no text for `grep` to match.
