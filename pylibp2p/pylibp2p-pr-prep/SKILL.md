---
name: pylibp2p-pr-prep
description: >
  Pre-flight validation, newsfragment generation, and PR readiness checklist for the py-libp2p repository.
  Validates towncrier newsfragments, runs lint/typecheck/tests via `make pr`, checks docs via `make linux-docs`,
  and audits public interface compliance (`INetwork`, `ITransport`, `IStream`, `IMuxedConn`).
  Use when the user says "prepare PR for py-libp2p", "create newsfragment", "check py-libp2p PR",
  "run make pr", "validate newsfragments", or is preparing to submit a PR to py-libp2p.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment with bash access"
license: MIT
---

# py-libp2p PR Preparation & Pre-Flight Validator

Automates the rigorous pre-flight checks required by the `py-libp2p` CI pipeline. Validates mandatory towncrier newsfragments, runs formatting, typechecks with mypy, executes tests, and verifies public interface integrity before pushing to upstream.

---

## Trigger Phrases

| User Input | Target Action |
|---|---|
| "Prepare PR for py-libp2p" / "Check my py-libp2p PR" | Run full pre-flight audit: newsfragments, `make pr`, git status |
| "Create newsfragment for issue #1428" | Generate formatted `<ISSUE_NUMBER>.<TYPE>.rst` with ReST rules |
| "Validate newsfragments" | Run `python ./newsfragments/validate_files.py` and towncrier check |
| "Check if my changes break py-libp2p interfaces" | Audit `libp2p/abc.py` for interface method regressions |

---

## Step 1: Newsfragment Management (CI Blocker)

Newsfragments are **mandatory** for every PR in `py-libp2p`. Missing or malformed fragments will fail CI immediately.

### Naming Convention
```text
newsfragments/<ISSUE_OR_PR_NUMBER>.<TYPE>.rst
```

### Valid Fragment Types
| Type | Description |
|---|---|
| `feature.rst` | New user-facing capability or API addition |
| `bugfix.rst` | Fix for a bug or incorrect behavior |
| `breaking.rst` | Breaking change to public interfaces or behavior |
| `deprecation.rst` | Deprecating an existing API or feature |
| `docs.rst` | Documentation improvements, guides, or docstrings |
| `performance.rst` | Performance or memory optimizations |
| `removal.rst` | Removal of previously deprecated functionality |
| `internal.rst` | Internal refactoring, CI updates, test improvements |
| `misc.rst` | Trivial fixes, dependency updates, typo corrections |

### Critical Content Rules
1. **User-Facing Perspective:** Write what changed from the perspective of a library user, not internal code details.
   - ❌ Bad: *"Added helper method `_read_frame` in yamux stream."*
   - ✅ Good: *"Fixed a bug in Yamux where incoming short frames could cause premature stream truncation."*
2. **ReStructuredText Syntax:** Use valid ReST markup. Backticks around code: `` `Yamux` ``.
3. **Mandatory Trailing Newline:** The `.rst` file MUST end with an explicit newline character (`\n`).

### Validation Command
```bash
# Run inside py-libp2p root
python ./newsfragments/validate_files.py
towncrier build --draft --version preview
```

---

## Step 2: Full Build & Verification Pipeline

Run the exact target executed by GitHub Actions:

```bash
# Activate repository virtual environment
source .venv/bin/activate || source venv/bin/activate

# Execute full verification: clean -> fix -> lint -> typecheck -> test
make pr
```

### Targeted Fixes for Common Failures:
- **Lint / Formatting issues:** Run `make fix` (applies ruff and autofixes).
- **Typecheck errors:** Run `make typecheck` (invokes `mypy --config-file tox.ini -p libp2p`).
- **Targeted Test Execution:** If full `make test` is too slow, run the specific test file:
  ```bash
  pytest tests/core/host/test_ping.py -v
  ```

---

## Step 3: Documentation Build Check

If changes touch `.rst`, `.md`, docstrings, or anything under `docs/`:

```bash
make linux-docs
```

Ensure no Sphinx warnings are treated as errors (`-W`).

---

## Step 4: Interface Compliance Check

Inspect changes against `libp2p/abc.py`. Public interfaces must maintain compatibility:

| Interface Class | Responsibility |
|---|---|
| `INetwork` | Network layer, peer connections, listeners |
| `ITransport` | Transport dialing and listening (TCP, QUIC, WS) |
| `IListener` | Inbound transport listener |
| `INetStream` / `IStream` | Multiplexed stream read/write/close |
| `IMuxedConn` | Stream multiplexer connection (Yamux, Mplex) |
| `ISecureConn` | Authenticated, encrypted channel (Noise, TLS) |
| `IPeerStore` | Peer addresses, keys, and protocols repository |

> [!WARNING]
> Renaming or removing any method on these interfaces is a **breaking change** and requires a `.breaking.rst` newsfragment and major/minor semver bump.

---

## Output Template for Review

```markdown
# 🚀 py-libp2p Pre-Flight Report

- **Branch:** `<branch-name>`
- **Linked Issue(s):** `#<issue-number>`
- **Newsfragment:** `newsfragments/<number>.<type>.rst` (✅ Validated / ❌ Missing)

### Verification Summary
| Check | Command | Status | Notes |
|---|---|---|---|
| **Newsfragments** | `python ./newsfragments/validate_files.py` | ✅ Passed | Valid towncrier draft |
| **Code Formatting** | `make fix && make lint` | ✅ Passed | Ruff formatting clean |
| **Type Checking** | `make typecheck` | ✅ Passed | Mypy 0 errors |
| **Test Suite** | `pytest tests/...` | ✅ Passed | All relevant tests passing |
| **Interface ABI** | `libp2p/abc.py` check | ✅ Untouched | No public breaking changes |

### Next Steps to Push
```bash
git add newsfragments/ <changed-files>
git commit -m "fix(yamux): handle short reads on stream (#<issue>)"
git push origin <branch-name>
```
```
