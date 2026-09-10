# 02 — Reading, searching, and shell input/output

## Create sample text

Start at the repository root:

```bash
cd labs/file-operations
mkdir -p scratch
printf 'INFO starting\nERROR missing file\nINFO finished\n' > scratch/events.log
```

This is invented log data. Repeating `printf` replaces this sample file.

## Read files with cat

`cat` means concatenate: it writes the contents of one or more files to standard
output in the order you supply them. It does not add headings or separators.
Its syntax is `cat [options] [file...]`.


Run from `labs/file-operations/`, after creating the sample above:

```bash
cat scratch/events.log
cat -n scratch/events.log
cat practice/src/test_copy.py
```

The first command prints the three sample lines. `-n` adds line numbers to the
output without editing the file. The supplied `test_copy.py` is empty, so the last
command normally prints nothing and succeeds. An empty result is different from
a missing file, which produces an error. Use `stat practice/src/test_copy.py` to
inspect its metadata; see the [stat guide](05-file-metadata-with-stat.md).

### Useful options for inspecting text

| Option | What it does |
| --- | --- |
| `-n` | Numbers every output line, including empty lines |
| `-b` | Numbers only nonempty lines; overrides `-n` |
| `-s` | Reduces consecutive empty lines to a single empty line |
| `-T` | Displays tab characters as `^I` |
| `-E` | Displays `$` at line endings |
| `-A` | Combines `-vET` to show nonprinting characters, line endings, and tabs |

Try a separate sample; rerunning `printf` replaces this scratch file:

```bash
printf 'name\trole\n\n\nSam\tlearner\n' > scratch/columns.txt
cat -n scratch/columns.txt
cat -b scratch/columns.txt
cat -s scratch/columns.txt
cat -A scratch/columns.txt
```

The sample has four lines, including two empty ones. `-n` numbers all four; `-b`
numbers only the two nonempty lines; `-s` leaves one empty line between them.
`-A` displays the first line as `name^Irole$`. These markers are a display aid;
they are not inserted into the source. Windows CRLF line endings appear as `^M$`
with `-A`. A line containing spaces is not an empty line for `-b` or `-s`.

### Combine files and save output

```bash
printf 'first file\n' > scratch/part-one.txt
printf 'second file\n' > scratch/part-two.txt
cat scratch/part-one.txt scratch/part-two.txt > scratch/combined.txt
cat scratch/combined.txt
```

This prints `first file` followed by `second file` on the next line. If the first
file lacked its final newline, the contents would meet on the same line: `cat`
does not invent a separator. The shell's `>` saves the output and replaces an
existing destination; `>>` appends instead. Keep the destination separate from
every input file. See the redirection section below for details.

### Read standard input and handle unusual filenames

With no filename, `cat` reads standard input. If you run `cat` alone interactively,
it waits for input; it has not frozen. Type a line and press Enter to have it
written back. Press `Ctrl+D` on an empty input line to signal end of input, or
`Ctrl+C` to interrupt it. A filename argument of `-` also means standard input:

```bash
printf 'from stdin\n' | cat -n
```

Quote filenames with spaces, for example `cat "my notes.txt"`. For a file named
`-draft.txt`, use `cat -- -draft.txt` or `cat ./-draft.txt`; `--` ends option parsing.
To read an actual file named `-`, use `cat ./-`, since plain `-` means stdin.
These filename examples assume those files exist.

Use `cat` for small text files. Use `less` for long text, and `file` to identify
unknown file types before displaying them; binary data can include terminal
control characters. `cat --help` lists the GNU options used here. Continue with
[viewing and editing file content](06-viewing-and-editing-content.md) for `less`,
`nano`, Vim, `tee`, and safe `sed` exercises.

## Preview, page through, and count text

```bash
head -n 2 scratch/events.log
tail -n 1 scratch/events.log
wc -l scratch/events.log
less scratch/events.log
```

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

## Redirection and pipes

Commands commonly read standard input (stdin), write results to standard output
(stdout), and write diagnostics to standard error (stderr). The shell connects
or redirects these streams.

| Syntax | Effect |
| --- | --- |
| `command > file` | Writes stdout to a file, creating or truncating it |
| `command >> file` | Appends stdout, creating the file if needed |
| `command 2> file` | Writes stderr to a file, creating or truncating it |
| `command < file` | Reads stdin from a file |
| `first \| second` | Connects the first command's stdout to the second's stdin |

```bash
printf 'INFO checked\n' >> scratch/events.log
grep 'INFO' scratch/events.log | wc -l
wc -l < scratch/events.log
```

After one append, the pipeline prints `3` (INFO matches), and the final command
prints `4` (all newlines). Repeating the append increases both counts. Pipes carry
data, not filenames; stderr is not included by default.

Avoid `cat file > file`: the shell truncates the destination before `cat` reads
it. Use a different output path when saving transformed text.

## Help and exit statuses

```bash
ls --help
help cd
grep 'NOT_PRESENT' scratch/events.log
echo $?
```

Many GNU commands support `--help`. `cd` is a Bash builtin, so use `help cd`.
`man ls` opens its manual if manuals are installed; press `q` to leave.
`$?` holds the previous command's exit status, so inspect it immediately. For
`grep`, `0` means a match, `1` means no match, and `2` means an error. Generally,
zero means success and nonzero indicates another outcome or failure, depending
on the command.

## Check your understanding

1. Why might a filename search find files that a content search does not?
2. Predict what changes if you replace `>>` with `>` in the append example.
3. Record a command, its output, and your explanation in the learning log.
4. How do `cat -n`, `cat -b`, and `cat -A` differ? Do they edit the source file?
5. Why might `cat` wait for input, and how do you finish the input?
