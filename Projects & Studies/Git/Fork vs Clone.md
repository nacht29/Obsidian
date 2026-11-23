## Important

### Difference

- Clone copies repo locally.
- Clone does not grant push permission.
- Fork creates your own remote copy on GitHub.
- If you have write access, push branch to original repo.
- If you do not have write access, fork first, push branch to fork, then open PR.

### Fork workflow

```sh
git clone <fork-url>
cd repo
git checkout -b my-feature
git push -u origin my-feature
```
