# Test fixtures

These files are **intentionally malicious**. They exist for the
self-test in `.github/workflows/test.yml`.

- `evil.pkl` — pickle with an `os.system` payload via `__reduce__`.
  Used to verify ml-guard correctly detects RCE patterns and the
  action exits with code 1 when configured with `fail-on: critical`.

- `clean/safe.pkl` — benign pickle (a dict with a small list).
  Used to verify the action exits 0 when nothing is wrong.

**Do not `pickle.load()` `evil.pkl` outside the static analysis
context** — it would execute `os.system("echo pwned")`. ml-guard
itself never executes scanned files.
