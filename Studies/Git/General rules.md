## Important

### General rules

- Prefer `git fetch` before making changes.
- Check current branch before committing or pushing.
- Do not rewrite shared branch history casually.
- Use `--force-with-lease`, not `--force`, after rebasing a pushed personal branch.
- Commit small logical changes.
- Resolve conflicts by editing files, staging resolved files, then continuing the merge or rebase.
- Use `git status` constantly.

### Quick commands

```sh
git status
git branch --show-current
git branch -a
git log --oneline --graph --decorate --all
```
