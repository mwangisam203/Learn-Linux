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
