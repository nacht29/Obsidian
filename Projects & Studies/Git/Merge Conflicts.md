## Important

### Core idea

- Git merges diffs, not whole folders.
- `git add .` does not automatically cause old files to overwrite newer files.
- If branch B did not modify `pipeline.py`, merging B will not revert A's newer `pipeline.py`.
- Conflict occurs when both branches edit overlapping lines.

### Merge conflict workflow

```sh
git fetch origin
git merge origin/main
# edit conflicted files
git add conflicted_file.py
git commit
```

### Rebase conflict workflow

```sh
git fetch origin
git rebase origin/main
# edit conflicted files
git add conflicted_file.py
git rebase --continue
```

### Abort commands

```sh
git merge --abort
git rebase --abort
```
