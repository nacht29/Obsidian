## Quick references

### Update feature branch with latest main using merge

```sh
git fetch origin
git checkout my-feature
git merge origin/main
```

### Update feature branch with latest main using rebase

```sh
git fetch origin
git checkout my-feature
git rebase origin/main
git push --force-with-lease
```

### Merge main into stage

```sh
git fetch origin
git checkout stage
git merge origin/main
# resolve conflicts if any
git add .
git commit
```

### Inspect remote branch

```sh
git fetch origin
git checkout -b local-name origin/remote-name
```

### Undo latest local commit but keep changes staged

```sh
git reset --soft HEAD~1
```

### Undo latest local commit but keep changes unstaged

```sh
git reset --mixed HEAD~1
```

### Abort merge or rebase

```sh
git merge --abort
git rebase --abort
```

### Combine commits
```sh
git reset --soft HEAD~2
git commit <message>
git push --force-with-lease
```