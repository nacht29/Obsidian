## Important

### Core idea

- PR = push branch to remote, then request merge into base branch.
- Base branch can be `main`, `stage`, etc.
- You can inspect a PR locally before merging.
- Branch name does not grant permission.
- You need push access to the remote branch source.

### Typical PR workflow

```sh
git checkout -b feature/my-change
git add .
git commit -m "Describe change"
git push -u origin feature/my-change
```

Then open PR on GitHub.

### Inspect PR branch locally

```sh
git fetch origin
git checkout -b review-branch origin/pr-branch-name
```
