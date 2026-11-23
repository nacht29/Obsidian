## Important

### Core concepts

- Commit = snapshot + metadata + parent reference.
- Branch = movable pointer/ref to a commit.
- HEAD = current checked-out commit or branch.
- Index = staging area.
- Working tree = actual files.
- Remote-tracking branch = local record of remote branch state, e.g. `origin/main`.
- Each repo has its own `.git/objects`.
- Push, pull and fetch transfer missing objects.
- Push checks ancestry, not whether files look similar.
- Merge uses 3-way merge: base, ours, theirs.
- Conflict usually happens when both sides edit overlapping lines.

### Flow

```text
Working tree -> git add -> Index -> git commit -> Local branch -> git push -> Remote branch
```
