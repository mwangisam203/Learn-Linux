# File operations lab

Run lesson commands from the directory stated in each note. This lab contains:

- `practice/data.csv` for field extraction and sorting.
- `practice/logs/app.log` for content searches and severity counts.
- `practice/src/` for nested-path and filename exercises.
- `practice/docs/` for moves and the repository-root `docs_link` symlink.
- `backups/Test.py` for permission inspection; it is intentionally empty.
- `test_main_hard.py` for file-identity discussions.

Use `scratch/` for output that does not need to be committed:

```bash
mkdir -p scratch
printf 'practice\n' > scratch/example.txt
cat scratch/example.txt
stat scratch/example.txt
```

Empty Python and configuration fixtures are deliberate. Extensions are part of a
name; they do not guarantee content, syntax, or executability. Confirm all three
with `file`, a suitable validator, and permissions when appropriate.
