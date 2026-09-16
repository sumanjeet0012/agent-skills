---
name: code-review-critique
description: >
  Structured code review for any snippet, file, function, git commit, or git diff.
  Finds bugs, edge cases, complexity issues, security flaws, resource leaks, and non-idiomatic code,
  then delivers a prioritized critique with actionable diffs and explanations.
  Use when the user asks to "review this code", "is this code correct", "critique my solution",
  "check my diff", "code review", "find bugs in this function", or pastes code asking for feedback.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment with shell or file tools"
license: MIT
---

# Code Review & Critique

A rigorous, senior-engineer code review system designed to find subtle bugs, ensure production reliability, optimize algorithms, and elevate code craft while providing actionable, educational feedback.

---

## Trigger Phrases

| User Input | Context & Intent |
|---|---|
| "Review this code" / "Critique my solution" | Comprehensive review of a snippet, file, or function |
| "Check my diff" / "Review my changes" | Repository diff review (`git diff` / staged changes / branch comparison) |
| "Is this code correct?" / "Find bugs in this" | Deep correctness, logic, edge-case, and boundary analysis |
| "Optimize this code" / "Check time complexity" | Algorithmic complexity, memory, and performance audit |
| "Security audit of this function" | Security vulnerability & input validation review |

---

## Execution Workflow

### Step 0: Scope & Context Detection

1. **Identify the Source:**
   - **Pasted snippet / function:** Review as-is; note any assumptions about missing dependencies or context.
   - **Local file path:** Read the file, inspect sibling imports, and locate associated test files (e.g., `tests/`, `__tests__/`, `*_test.go`).
   - **Git repository diff:** Run `git status`, followed by:
     - Unstaged changes: `git diff`
     - Staged changes: `git diff --cached`
     - Branch changes: `git diff origin/main...HEAD` (or `master` if `main` does not exist)
     - Specific commit: `git show <commit-hash>`
     *Always inspect surrounding file lines to verify context and avoid false positives.*

2. **Detect Context & Purpose:**
   - **Production code:** Highest bar — strict error handling, security, logging, concurrency safety, clean API boundaries, and comprehensive unit tests.
   - **Interview / Competitive Programming:** Focus primarily on algorithmic correctness, edge cases, time/space complexity bounds, and clean brevity. Omit boilerplate enterprise checks unless requested.
   - **Script / Learning code:** Focus on readability, idiomatic idioms, simplicity, and core correctness.
   *(If ambiguous, infer from code style or state assumptions upfront).*

3. **Verify via Execution (When Safe & Available):**
   - If a shell/execution tool is available, execute a fast scratch test or linter to verify failing inputs before reporting. Never guess when you can test.

---

### Step 1: Six-Axis Analysis

Examine silently across all six dimensions before generating findings:

1. **Correctness & Logic:**
   - Off-by-one errors, loop bounds, inverted conditionals, broken base cases.
   - Unhandled null/nil/None/undefined references and empty collections.
   - Mutable default arguments (Python), unintended variable shadowing, variable closure capture.
   - Silent failures (e.g., swallowed exceptions, ignored error return values in Go/Rust).
2. **Edge Cases & Boundaries:**
   - Empty input (`""`, `[]`, `null`, `{}`), single-element inputs, identical/duplicate elements.
   - Numerical limits: integer overflow/underflow (e.g., `(l + r) / 2` in binary search), division by zero, float precision drift (`0.1 + 0.2 != 0.3`).
   - String boundaries: Unicode/multibyte characters, leading/trailing whitespace, unexpected delimiters.
3. **Complexity & Performance:**
   - Algorithmic Big-O: State Time and Space complexity (Best / Average / Worst).
   - Accidental quadratic loops: e.g., calling `list.contains()` or `list.indexOf()` inside a loop instead of using a Set/Map.
   - Redundant recomputation: missing memoization, unindexed database queries, unnecessary object allocations in hot loops.
4. **Resource & Concurrency Safety:**
   - Leaks: unclosed database connections, unclosed file descriptors, lingering sockets.
   - Concurrency: race conditions, deadlocks, goroutine leaks, unawaited async coroutines (`asyncio` in Python, unhandled Promises in JS/TS), unsafe shared mutable state without synchronization.
5. **Security & Input Validation:**
   - Injection vulnerabilities: SQL injection, shell command injection, HTML/XSS injection, path traversal (`../`).
   - Secrets: hardcoded credentials, API keys, JWT secrets, private keys.
   - Input hygiene: unvalidated lengths, untrusted deserialization (`pickle.loads`, `yaml.load`), insecure randomness for tokens (`Math.random` / `random.random` instead of CSPRNG).
6. **Craft & Idiomatic Practices:**
   - Meaningful naming that reveals intent; no misleading or cryptic variable names.
   - Adherence to language idioms:
     - **Python:** PEP 8, list comprehensions, context managers (`with`), explicit exceptions instead of bare `except:`.
     - **Go:** Explicit `if err != nil` handling, proper defer usage, context propagation, avoiding goroutine leaks.
     - **TypeScript/JavaScript:** Strict equality `===`, async/await without unhandled rejections, immutability where appropriate.
     - **Java:** No raw types, avoiding `==` on boxed objects (use `.equals()`), try-with-resources, StringBuilder in loops.
     - **Rust:** Proper `Result`/`Option` handling without indiscriminate `.unwrap()`, borrow checker best practices.

---

### Step 2: Output Format

Format the review using this structured, scannable template:

```markdown
# 🔍 Code Review: <Component / Function / Diff Name>

## Executive Summary
1–2 sentences summarizing the overall quality, key risks, and primary recommendations.

---

## 🔴 Critical (Must Fix — Bugs, Security, Crashes)
> Items that cause incorrect behavior, security vulnerabilities, memory leaks, or data loss.

- **[file:line or Symbol Name]** — Brief summary of issue
  - **Problem:** What is broken and why.
  - **Failing Scenario:** Concrete input, sequence, or trigger (e.g., `Input: nums = [1], target = 0 -> throws IndexOutOfBoundsException`).
  - **Fix:** Direct actionable code diff or remedy.

*(If none, state: "✅ No critical issues found.")*

## 🟡 Warnings (Should Fix — Performance, Fragility, Edge Cases)
> Items that degrade performance, break under uncommon edge cases, or violate core invariants.

- **[file:line or Symbol Name]** — Brief summary of issue
  - **Impact:** What happens under load or edge cases (e.g., accidental $O(N^2)$ scaling).
  - **Recommended Fix:** How to address it cleanly.

*(If none, state: "✅ No warnings found.")*

## 🔵 Craft & Idioms (Suggestions — Readability, Maintainability)
> Idiomatic patterns, naming clarity, dead code removal, or documentation improvements.

- **[file:line]** Specific suggestion in 1–2 lines.

## ✅ What's Good
Highlight 1–3 genuine strengths (e.g., clean decomposition, effective data structure choice, thoughtful comments). Never fabricate praise.

---

## ⚡ Complexity & Resource Analysis
| Dimension | Current Implementation | Optimal Target | Notes / Gap |
|---|---|---|---|
| **Time Complexity** | $O(...)$ | $O(...)$ | Explanation of bottleneck |
| **Space Complexity** | $O(...)$ | $O(...)$ | Memory footprint & allocations |

---

## 🛠️ Recommended Changes

```diff
// Use diff syntax for concise changes, or language-specific snippet with inline comments
- original_line()
+ corrected_line() // fix: explanation
```

*(If extensive refactoring is needed, provide the complete revised implementation using appropriate comment syntax for the target language: `#` for Python/Ruby/Shell, `//` for Go/Java/C++/TS, `--` for SQL).*

---

## 🎓 Key Takeaways & Lessons
- Bullet list of transferable rules and patterns to remember for future implementations (e.g., *"Always use `l + (r - l) / 2` to avoid integer overflow in languages with fixed-width integers"*).
```

---

## Rules & Quality Standards

1. **Evidence-Based Findings:** Never make vague accusations like "this might fail". Provide the concrete failing input, call stack, or edge case. If a concern cannot be confirmed, place it under an explicit "Unverified Concerns" note.
2. **Impact-Driven Severity:** Severity is determined by runtime consequence, never personal aesthetic preference. Whitespace or minor stylistic preferences are strictly 🔵 Suggestions.
3. **Respect Established Project Conventions:** When reviewing within an existing repository, match surrounding architecture, naming conventions, and styling.
4. **Actionable Diffs:** Prefer targeted diff blocks showing before/after lines rather than dumping 300 lines of unchanged code.
5. **Honest Brevity:** If the code is well-written and correct, state it clearly and keep the review concise. Do not invent flaws to appear thorough.
