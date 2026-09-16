---
name: pylibp2p-pr-gate
description: >
  Strict PR-ready gate for the py-libp2p repository. Blocks any push/PR until three gates pass
  on committed state: (1) full lint via `pre-commit run --all-files`, (2) the full test suite,
  (3) docs generation via `make check-docs`. Enforces venv parity with pinned dev dependencies
  and forbids scoped (`--files`) verification. Use when the user says "is the PR ready",
  "push the changes", "create the PR", "ready for review", or asks to verify py-libp2p work
  before pushing to upstream.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment with bash access"
license: MIT
---

# py-libp2p PR Gate: Lint + Tests + Docs Must Pass Before Push

No commit is pushed and no PR is opened until **all three gates pass, in order,
on committed state**. Scoped checks (`--files`, single test files) are triage
tools only — they never qualify a push.

---

## Step 0: Commit First, Then Verify (non-negotiable order)

`pre-commit` **stashes unstaged changes** while hooks run. Verifying uncommitted
work means the hooks check HEAD without your fixes (stale results that lie).

```bash
git add -A && git commit -m "<msg>"   # commit FIRST
git status --short                     # must be clean before gates
```

## Step 1: Venv Parity With Pinned Dev Deps

A drifted venv makes local checkers disagree with CI (phantom errors locally,
missed errors that CI then catches). Align before gating:

```bash
.venv/bin/pip install -q "factory-boy>=2.12.0,<3.0.0" "rich>=13.0" \
  "prompt_toolkit" "msgpack" "redis" "p2pclient>=0.3.0"
```

Known drift signatures (all mean "fix the venv, not the code"):

| Symptom | Cause |
|---|---|
| pyrefly `bad-override` in `tests/utils/factories.py`, `Never` errors in gossipsub tests | `factory-boy 3.x` installed, project pins `<3.0.0` |
| pyrefly `Could not find import` in `examples/` | missing `rich` / `prompt_toolkit` / `msgpack` / `redis` |
| pyrefly `Could not find import p2pclient` | missing pinned test dep `p2pclient>=0.3.0` |

## Step 2: Gate 1 — Full Lint (CI's tox lint env runs exactly this)

```bash
pre-commit run --all-files
```

This covers pyupgrade, ruff, ruff-format, mypy-local, pyrefly-local, RST checks,
and the path audit. **Every hook must pass.** Common gotchas:

- A hook that *rewrites* files (e.g. pyupgrade on regenerated `*_pb2_grpc.py`)
  exits 1 — commit its output and re-run.
- `getattr`-based capability probes confuse pyrefly (`object is not awaitable` /
  `not iterable` / `Cannot index into object`). Narrow with `cast(Any, ...)` at
  the use site or explicit `Any` annotations — never silence with `# type: ignore`
  unless no narrowing is possible.
- Generated `*_pb2*.py` / `*.pyi` are ruff-excluded by project config but NOT
  from mypy/pyrefly: hand-fix generator artifacts the project actually checks
  (e.g. bare `Mapping` generics in `.pyi`).
- `hasattr(module, "WINDOWS_ONLY_ATTR")` fails typecheck on Linux CI — use
  `getattr(module, "ATTR", None)` instead.

## Step 3: Gate 2 — Full Test Suite

```bash
.venv/bin/python -m pytest tests -n auto -m "not serial_only" -q
```

(CI runs the same via `make test`, plus serial-only and platform jobs.)
Zero failures. If a failure looks environmental, re-run it in isolation before
calling it a flake — twice.

## Step 4: Gate 3 — Docs Generation

```bash
make check-docs
```

(NOT `make linux-docs` — it calls `xdg-open`, which fails headless.)
Sphinx warnings are errors (`-W`); missing newsfragments fail here too.

## Step 5: Push Only When Gates 1–3 Are Green

```bash
git status --short   # clean: only the intended commits exist
git push origin <branch>
```

After pushing, watch the PR's CI (lint on all Python versions + docs first —
they're fastest). If CI fails on something local gates missed, append the case
to this skill's gotcha lists so the gate gets stricter.

## Output Template

```markdown
# 🚦 py-libp2p PR Gate Report — `<branch>`
- Venv parity: ✅ aligned / ❌ drifted (fixed: ...)
- Gate 1 lint (`pre-commit run --all-files`): ✅ / ❌ (...)
- Gate 2 tests (full suite): ✅ N passed / ❌ (failures: ...)
- Gate 3 docs (`make check-docs`): ✅ / ❌ (...)
- Verdict: PUSHED (`<sha>`) / BLOCKED (reason + fix)
```
