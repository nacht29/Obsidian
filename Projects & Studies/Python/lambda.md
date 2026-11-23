
`lambda` creates an anonymous, single-expression function.

```python
lambda arg1, arg2: expression
```

- No `return` keyword — the expression's value is returned automatically.
- Can take any number of args (including defaults, `*args`, `**kwargs`), but body must be **one expression**, no statements (no `=`, `if:`, loops, etc.).
- Assign to a name if reused:

```python
square = lambda x: x**2
square(4)  # 16
```

- Common use: inline callback for `sorted`, `map`, `filter`, `key=`:

```python
sorted(data, key=lambda x: x[1])
list(filter(lambda x: x % 2 == 0, nums))
```
