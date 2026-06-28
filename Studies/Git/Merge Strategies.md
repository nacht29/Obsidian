## Quick references

### Methods

| Method | Use case | Notes |
| --- | --- | --- |
| Normal merge | Preserve branch history | May create merge commit |
| `--ff-only` | Only allow fast-forward | Fails if merge commit needed |
| `--squash` | Bring changes as one commit | Does not preserve branch commits |
| `cherry-pick` | Take specific commit | Useful for selective changes |
| `revert` | Undo safely on shared branch | Creates inverse commit |

### Commands

```sh
git merge feature-branch
git merge --ff-only feature-branch
git merge --squash feature-branch
git cherry-pick <commit-sha>
git revert <commit-sha>
```
