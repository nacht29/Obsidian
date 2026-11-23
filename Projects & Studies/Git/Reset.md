## Important

### Core idea

- `reset` moves HEAD.
- It is useful for undoing local commits.
- Be careful with shared branches.

### Reset modes

| Command                    | Commit removed? | Changes kept? | Staged? |
| -------------------------- | --------------- | ------------- | ------- |
| `git reset --soft HEAD~1`  | Yes             | Yes           | Yes     |
| `git reset --mixed HEAD~1` | Yes             | Yes           | No      |
| `git reset --hard HEAD~1`  | Yes             | No            | No      |

### Commands

```sh
git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1
```

### Important note

`--hard` discards working tree changes. Use carefully.
