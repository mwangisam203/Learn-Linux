# Linux Fundamentals Learning Lab

My workspace for learning Linux through explanations and hands-on exercises.
**All application files, configurations, backups, and Python files are made-up
practice data.** Empty files are intentional; this is not a working application.

## Folder structure

```text
.
├── README.md                   # Purpose, folder map, and where to begin
├── notes/                      # Actual study notes
│   ├── 01-navigation-and-files.md
│   ├── 02-reading-and-searching.md
│   ├── 03-permissions-and-links.md
│   ├── learning-log.md
│   └── original-link-exercise.md
└── labs/                       # Files to inspect and manipulate
    ├── file-operations/
    │   ├── backups/            # Dummy backup files
    │   ├── test_main.py        # Empty practice file
    │   └── practice/
    │       ├── .env            # Empty hidden file
    │       ├── config.json     # Empty configuration prop
    │       ├── docs/notes.txt  # Dummy text file, not study notes
    │       ├── logs/           # Initially empty
    │       └── src/            # Dummy Python files and utils/
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

Each note states where to run its examples. Run commands individually and observe
the results. Commands affect the paths you give them, even in a practice repository.
