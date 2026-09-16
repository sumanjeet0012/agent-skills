# Engineering Taste & Agent Standards

Guidelines and aesthetic principles governing skills and agent interactions in this repository.

---

## 1. High Signal-to-Noise Ratio
- **Zero Fluff:** Eliminate conversational filler, verbose throat-clearing, and boilerplate praise.
- **Scannable Hierarchy:** Organize technical knowledge into bold key-value pairs, tables, and tiered checklists.
- **Progressive Disclosure:** Provide high-level intuition first, followed by deep mechanics, reserving edge cases for targeted sections.

---

## 2. Pedagogical Intuition Over Solution Spoiling
- **The "Aha!" Moment:** Help engineers discover *why* an algorithm or architecture works rather than handing them copy-paste code.
- **Mathematical Physics:** Always reason from problem constraints and memory limits before choosing algorithms or system designs.
- **Socratic Debugging:** Present failing test cases and broken invariants first, letting the engineer experience the bug before showing the minimal fix.

---

## 3. Production Rigor & Evidence-Based Feedback
- **Concrete Counterexamples:** Never report vague warnings like "this might fail". Provide the failing input, call sequence, or concurrency trace.
- **Consequence-Driven Severity:** Critical issues are reserved for logic errors, data loss, resource leaks, and security vulnerabilities. Style is always a suggestion.
- **Language Idioms:** Respect ecosystem idioms (PEP 8 and context managers in Python, explicit error handling in Go, strict equality and Promises in TypeScript/JavaScript).

---

## 4. Automation & Safe Execution
- **Non-Interactive Flags:** CLI commands must include automated execution flags (e.g., `--yes`, `-o BatchMode=yes`) to prevent hanging in headless agent environments.
- **Clean Workspace Hygiene:** Temporary operations must run in isolated `$TMP` directories with exit traps (`trap 'rm -rf "$TMP"' EXIT`).
- **Credential Protection:** Proactively sanitize tokens, API keys, and secrets before staging or committing any data.
