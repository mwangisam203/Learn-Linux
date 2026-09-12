# 08 — Linux fundamentals review drills

Run these from the repository root. Predict the result first, then record the
command, output, and explanation in the learning log. Use only practice paths.

## Navigation and paths

1. Print the current directory, enter `labs/file-operations`, and return using one command.
2. Find every `.py` path under `labs` without matching letter case.
3. Display the stored target and resolved absolute target of `labs/links/soft.txt`.

## Viewing and text processing

4. Display the first three records in `practice/logs/app.log` with line numbers.
5. Count ERROR lines without printing them.
6. Print only names and departments from `practice/data.csv`.
7. Produce a count for each log severity using `cut`, `sort`, and `uniq`.
8. Show the line before and after the warning entry.

## Metadata, links, and permissions

9. Prove whether `original.txt` and `hard.txt` currently share an inode.
10. Compare metadata for `soft.txt` itself and its resolved target.
11. Convert mode `750` into symbolic owner, group, and other permissions.
12. On a scratch copy, give the owner read/write, the group read, and others nothing.
13. Explain why removing a file depends mainly on its parent directory.

## Editing and verification

14. Create a three-line scratch file without opening an editor.
15. Preview a `sed` replacement, then apply it while retaining a `.bak` file.
16. Explain how you would verify content, metadata, and Git state afterward.

Answers are in [09-review-answers.md](09-review-answers.md). Attempt each drill
before opening them; being able to explain the result matters more than speed.
