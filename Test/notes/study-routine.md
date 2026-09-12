# Study routine

Use a short feedback loop instead of copying long command sequences blindly.

1. **Choose one concept.** Examples: relative paths, pipelines, or directory modes.
2. **Inspect the starting state.** Use `pwd`, `ls`, `stat`, and `git status`.
3. **Predict.** Write the expected output, path change, mode, or exit status.
4. **Run one command.** Keep the target inside `labs/` or a `mktemp` directory.
5. **Verify independently.** Use a different command to confirm the result.
6. **Explain every part.** Name the command, options, arguments, and shell operators.
7. **Record mistakes.** State the cause and correction in `learning-log.md`.
8. **Restore or keep intentionally.** Know whether the exercise altered tracked data.

A productive 25-minute session can include five minutes of review, fifteen minutes
of practice, and five minutes writing what happened. Revisit missed drills after a
day without looking at the answer first. Recall strengthens understanding more than
rereading the same explanation.

Stop when an unexpected command targets a broader path than intended. Inspect the
current directory and expand the path before trying again.
