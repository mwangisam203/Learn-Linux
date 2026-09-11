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

## Practice grep with the application log

The repository now includes an invented log at `practice/logs/app.log`. Run these
commands from `labs/file-operations/`:

```bash
grep -n 'ERROR' practice/logs/app.log
grep -in 'server' practice/logs/app.log
grep -En 'ERROR|WARNING' practice/logs/app.log
grep -c 'ERROR' practice/logs/app.log
grep -n -C 1 'High memory' practice/logs/app.log
```

| Option | Meaning |
| --- | --- |
| `-n` | Include the line number |
| `-i` | Ignore uppercase/lowercase differences |
| `-E` | Use extended regular expressions; `|` means “or” |
| `-c` | Print the number of matching lines instead of the lines |
| `-v` | Invert the selection and print nonmatching lines |
| `-w` | Require a whole-word match |
| `-A N`, `-B N`, `-C N` | Show N lines after, before, or around each match |
| `-l` | Print filenames containing at least one match |
| `-q` | Print nothing; communicate the result through the exit status |

The sample has three `ERROR` lines, so `grep -c 'ERROR'` prints `3`. `-c` counts
matching **lines**, not every occurrence within those lines. Quote patterns so the
shell does not interpret characters such as `*`, `?`, or `|` before `grep` does.

Use `-F` for literal text and `-E` when you deliberately need a regular expression:

```bash
grep -Fn '/api/users' practice/logs/app.log
grep -En '^(.* )?(ERROR|WARNING) ' practice/logs/app.log
```

The second expression is more complex than this log requires, but it demonstrates
`^` for the start of a line, parentheses for grouping, `|` for alternatives, `?`
for an optional group, and `.*` for any number of characters. Build expressions
in small steps and test them on practice data.

To search a directory recursively while limiting filenames:

```bash
grep -rn --include='*.log' 'ERROR' practice
```

`-r` descends into directories. `--include='*.log'` searches only names matching
the quoted shell-style pattern. Recursive searches can produce lots of output;
start from the narrowest useful directory and add `--exclude-dir` when necessary.

## Extract fields from the CSV sample

The made-up `practice/data.csv` file contains a header followed by four records:

```bash
column -s, -t practice/data.csv
cut -d, -f1 practice/data.csv
cut -d, -f1,3 practice/data.csv
tail -n +2 practice/data.csv | sort -t, -k3,3 -k1,1
```

- `column -s, -t` displays comma-separated fields as an aligned table when the
  `column` utility is installed. It does not modify the CSV file.
- `cut -d,` uses a comma as the delimiter. `-f1` selects the first field, while
  `-f1,3` selects the first and third fields. Fields are numbered from one.
- `tail -n +2` starts at line two, excluding the header before sorting.
- `sort -t,` treats commas as separators. `-k3,3` sorts by only the third field,
  then `-k1,1` uses the first field as a tie-breaker.

These simple commands do not fully parse CSV quoting rules. Use a CSV-aware tool
when fields can contain quoted commas, embedded newlines, or escaped quotation marks.

## Build text-processing pipelines

A pipeline passes each command's standard output to the next command's standard
input. Use small stages and inspect intermediate output while learning:

```bash
cut -d' ' -f3 practice/logs/app.log
cut -d' ' -f3 practice/logs/app.log | sort
cut -d' ' -f3 practice/logs/app.log | sort | uniq -c
grep 'ERROR' practice/logs/app.log | cut -d' ' -f1,2,4-
```

The first field-extraction pipeline ultimately counts three `ERROR`, three `INFO`,
and one `WARNING` line. `uniq -c` counts adjacent identical lines, which is why
`sort` comes first. `sort -u` is a shorter way to print unique sorted values when
you do not need counts.

In the final command, `grep` selects error records and `cut -f1,2,4-` prints the
date, time, and every field from the fourth onward, omitting the severity field.
With `cut -d' '`, repeated spaces create empty fields, so this works predictably
only because the supplied sample uses single spaces between its first fields.

Other useful transformations include:

```bash
tr '[:lower:]' '[:upper:]' < practice/data.csv
sed -n '2,4p' practice/data.csv
wc -l -w -c practice/logs/app.log
```

`tr` maps lowercase characters to uppercase on its output. `sed -n '2,4p'` prints
only lines two through four. `wc -l -w -c` reports newline, word, and byte counts.
These commands leave the source files unchanged because their output is still going
to the terminal. Redirect to a different file if you want to save a result.

## Understand pipeline exit statuses

By default, Bash normally reports a pipeline's status as the status of its final
command. During a script or careful debugging session, enable `pipefail` so a
failure in an earlier stage makes the pipeline fail:

```bash
set -o pipefail
grep 'ERROR' practice/logs/app.log | wc -l
echo "$?"
```

`$?` must be checked immediately. `grep` uses `0` for at least one selected line,
`1` for no selected lines, and `2` for an error. A no-match result is often normal,
so decide what each status means in the task you are performing.

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
6. Why does `uniq -c` normally follow `sort`?
7. How would you count only ERROR lines in the supplied log?
8. Why are `cut -d,` examples insufficient for every possible CSV file?
