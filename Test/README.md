# Linux Fundamentals Learning Lab

My workspace for learning Linux through explanations and hands-on exercises.
**All application files, configurations, backups, and Python files are made-up
practice data.** Empty files are intentional; this is not a working application.

## Folder structure

```text
.
├── README.md                   # Purpose, folder map, and where to begin
├── docs_link -> labs/file-operations/practice/docs
├── notes/                      # Actual study notes
│   ├── 01-navigation-and-files.md
│   ├── 02-reading-and-searching.md
│   ├── 03-permissions-and-links.md
│   ├── 04-copy-move-and-link-practice.md
│   ├── 05-file-metadata-with-stat.md
│   ├── 06-viewing-and-editing-content.md
│   ├── learning-log.md
│   └── original-link-exercise.md
└── labs/                       # Files to inspect and manipulate
    ├── file-operations/
    │   ├── backups/            # Dummy backup files
    │   ├── test_main_hard.py   # Remaining name from the file identity exercise
    │   └── practice/
    │       ├── .env            # Empty hidden file
    │       ├── config.json     # Empty configuration prop
    │       ├── docs/
    │       │   ├── notes.txt  # Dummy text file, not study notes
    │       │   └── file_backup.txt # Moved from backups/
    │       ├── logs/           # Initially empty
    │       └── src/            # Dummy Python files, test_copy.py, and utils/
    └── links/
        ├── original.txt
        ├── hard.txt           # Same inode as original.txt
        └── soft.txt -> original.txt
```

Git does not track empty directories, so `practice/logs/` may need recreating
after cloning. Tool-managed hidden directories are omitted from this map.

## Where to begin

Open a Bash terminal in the repository root and follow these notes in order:

1. [Navigation and file operations](notes/01-navigation-and-files.md)
2. [Reading, searching, and shell input/output](notes/02-reading-and-searching.md)
3. [Permissions, inodes, and links](notes/03-permissions-and-links.md)
4. [Copy, move, and link practice](notes/04-copy-move-and-link-practice.md)
5. [File metadata with stat](notes/05-file-metadata-with-stat.md)
6. [Viewing and editing file content](notes/06-viewing-and-editing-content.md)

Each note states where to run its examples. Run commands individually and observe
the results. Commands affect the paths you give them, even in a practice repository.

The latest session moved a backup into the mock docs folder, left a remaining test
filename and a separate copy, and added `docs_link` for directory navigation.
The fourth note explains the observed results. Hard-link identity is local to the
working filesystem; Git does not preserve it when cloning.

## README versus notes

| Location | What belongs here |
| --- | --- |
| `README.md` | Purpose, structure, how to start, and links to learning material |
| `notes/` | Concepts, syntax, flag explanations, examples, expected results, and mistakes |
| `notes/learning-log.md` | Personal observations, experiments, and questions |
| `labs/` | Dummy files used by exercises |

Keep detailed explanations in notes and link to them here. Your former README is
preserved in [original-link-exercise.md](notes/original-link-exercise.md). Its paths
assume the old layout; use the updated links note for runnable exercises.

## Topics to learn next

- Processes and signals: `ps`, `top`, `kill`
- Storage: `df`, `du`, mounting
- Users, groups, and ownership
- Packages, services, and logs
- Shell scripts, variables, and exit statuses
