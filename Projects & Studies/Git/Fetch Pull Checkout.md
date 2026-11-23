## Quick references

### Comparison

| Command | Meaning | Does it change files? |
| --- | --- | --- |
| `git fetch` | Downloads remote refs/objects | No |
| `git checkout` / `git switch` | Switch branch/commit | Yes |
| `git pull` | Fetch + merge by default | Yes |
| `git pull --rebase` | Fetch + replay local commits | Yes |

### Commands

```sh
git fetch origin
git merge origin/main
git rebase origin/main
git pull
git pull --rebase
```

### Important note

Prefer this while learning:

```sh
git fetch origin
git merge origin/main
```

Or:

```sh
git fetch origin
git rebase origin/main
```

This is clearer than blindly using `git pull`.
