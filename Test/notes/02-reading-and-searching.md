# 02 — Reading, searching, and shell input/output

## Create sample text

Start at the repository root:

```bash
cd labs/file-operations
mkdir -p scratch
printf 'INFO starting\nERROR missing file\nINFO finished\n' > scratch/events.log
```

This is invented log data. Repeating `printf` replaces this sample file.
