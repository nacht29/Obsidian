# os

**`os.environ["KEY"]`** — dict-like mapping of process env vars. Raises `KeyError` if missing. Use for required config (fail loudly).

**`os.environ.get("KEY", default)`** — `.get()` on that same mapping. Returns `default` (or `None`) instead of raising.

**`os.getenv("KEY", default)`** — module-level wrapper, functionally identical to `os.environ.get()`. Style preference only.

**Rule of thumb:** `os.environ["KEY"]` for required config, `.get()`/`getenv()` for optional config with a fallback.
