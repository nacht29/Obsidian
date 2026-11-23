## Comparison

| Feature | Rebase | Reset |
| --- | --- | --- |
| Main purpose | Rewrite/replay commits | Move HEAD/branch pointer |
| Control | Fine control with interactive rebase | Blunt pointer movement |
| Common use | Clean local commits | Undo local commits |
| Shared branch safety | Risky if already pushed | Risky if already pushed |

## Rules

- Use rebase to clean or reorganise local commits.
- Use reset to undo local commits.
- Use revert for shared branch undo.
