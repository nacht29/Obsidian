## Important

### Core idea

- Rebase replays commits on top of another commit.
- Rebase creates new commit hashes.
- Use rebase to clean local branch history before merging.
- During rebase conflict: resolve file, `git add`, then `git rebase --continue`.
- After rebasing a pushed branch, use `git push --force-with-lease`.

### Normal rebase workflow

```sh
git fetch origin
git rebase origin/main
git push --force-with-lease
```

### Interactive rebase

```sh
git rebase -i HEAD~3
```

### Interactive rebase actions

| Action | Meaning |
| --- | --- |
| `pick` | Keep commit |
| `reword` | Keep commit but edit message |
| `drop` | Remove commit |
| `squash` | Combine commit into previous commit |

### Example

Before:

```text
A -> B -> C -> D
```

After reword B and drop C:

```text
A -> B' -> D'
```
