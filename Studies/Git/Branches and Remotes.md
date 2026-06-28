## Important

### Local branch vs remote-tracking branch

- Local branch = branch you can directly commit to locally.
- Remote-tracking branch = local record of a branch from a remote.
- `origin/main` is not the same as local `main`.
- After fetch, a remote branch may appear as `origin/some-branch`, but it is not automatically a local branch.

### Inspect remote branch locally

```sh
git fetch origin
git checkout -b local-branch-name origin/remote-branch-name
```

### Current branch commands

```sh
git branch --show-current
git status
git branch -a
```
