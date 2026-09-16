---
name: session-notes-to-github
description: >
  Summarize the technical substance of the current conversation (topics discussed, concepts learned,
  architectural decisions, code snippets, and next steps) and publish it as a clean, structured Markdown note
  to a designated GitHub repository (defaults to sumanjeet0012/temporary).
  Use when the user asks to "save this discussion", "make a markdown of what we discussed",
  "upload notes to GitHub", "save session to temporary repo", or "push today's notes".
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment with git and shell access"
license: MIT
---

# Session Notes to GitHub

Captures the core substance, key decisions, code snippets, and takeaways from the active working session into an Obsidian-ready, high-signal Markdown document and securely pushes it to a remote GitHub repository.

---

## Trigger Phrases

| User Input | Target Action |
|---|---|
| "Save this discussion" / "Push today's notes" | Generate summary & push to default repo (`sumanjeet0012/temporary`) |
| "Make a markdown of what we discussed and push to github" | Full session documentation with code blocks & architectural takeaways |
| "Save notes to repo <owner/repo>" | Target a custom repository instead of the default |

---

## Execution Workflow

### Phase 1: Context Distillation & Sanitization

1. **Extract Factual Substance (Zero Padding):**
   - Review the actual conversation transcript for real discussions, decisions, and code changes.
   - Never invent topics, praise, or discussions that did not happen.
2. **Mandatory Secret Sanitization Check:**
   - Scan the drafted note for credentials: API keys, JWT tokens (`ey...`), bearer tokens, private keys (`BEGIN PRIVATE KEY`), and passwords.
   - Redact any discovered secrets with `[REDACTED_SECRET]` and document the redaction in the note header.
3. **Determine Exact Date:**
   - Execute `date +%Y-%m-%d` to get today's accurate local calendar date (e.g., `2026-09-04`).

---

### Phase 2: Document Structure Template

Format the Markdown note with frontmatter for Obsidian, static-site generators, and GitHub rendering:

```markdown
---
title: "<Topic — Short Descriptive Title>"
date: YYYY-MM-DD
tags:
  - session-notes
  - <topic-tag-1>
  - <topic-tag-2>
summary: "<1-2 sentence executive summary of what was accomplished or explored>"
---

# <Topic — Short Descriptive Title>

- **Date:** YYYY-MM-DD
- **Context:** <1-line summary of context, e.g., "Deep dive into Tailscale Funnel & Serve architecture">

---

## 📌 Executive Summary & Key Decisions
- Bullet points covering core architectural decisions, discoveries, and solutions established during the session.

---

## 🛠️ Concepts & Mechanics Explored
Detailed breakdown of technical mechanics, protocols, algorithms, or systems discussed:
- **Sub-topic A:** Core mechanics, trade-offs, and invariants.
- **Sub-topic B:** Relevant commands, configurations, or patterns.

---

## 💻 Key Code Snippets & Commands
Include the exact, working code snippets, CLI incantations, or configurations produced in the conversation:

```bash
# Important commands executed or recommended
```

```python
# Canonical code snippets developed
```

---

## 🔗 Resources & References Mentioned
- Direct verified URLs to documentation, repositories, RFCs, or articles referenced.

---

## 🚀 Open Questions & Next Steps
- [ ] Next action items identified by the user or agent.
- [ ] Unresolved questions or topics scheduled for future exploration.
```

---

### Phase 3: File Naming & Git Publishing

1. **Determine Target Repository:**
   - Default: `sumanjeet0012/temporary` (override if specified by user).
2. **Generate Standard File Name:**
   - Pattern: `YYYY-MM-DD-<kebab-case-topic>.md` (e.g., `2026-09-04-tailscale-architecture-notes.md`).
   - If the note targets the root directory (default in `sumanjeet0012/temporary`), keep it at repository root.
3. **Execute Safe Git Operations:**
   Run the following automated sequence in a clean temporary directory:

```bash
set -e

REPO="sumanjeet0012/temporary"
FILE_NAME="YYYY-MM-DD-<kebab-case-topic>.md"

TMP=$(mktemp -d)
trap 'rm -rf "$TMP"' EXIT

# 1. Clone using gh if authenticated, fallback to standard git
if command -v gh >/dev/null 2>&1 && gh auth status >/dev/null 2>&1; then
    gh repo clone "$REPO" "$TMP/repo"
else
    git clone "https://github.com/$REPO.git" "$TMP/repo" || git clone "git@github.com:$REPO.git" "$TMP/repo"
fi

# 2. Check for filename collisions
TARGET_PATH="$TMP/repo/$FILE_NAME"
COUNTER=2
BASE_NAME="YYYY-MM-DD-<kebab-case-topic>"
while [ -f "$TARGET_PATH" ]; do
    TARGET_PATH="$TMP/repo/${BASE_NAME}-${COUNTER}.md"
    FILE_NAME="${BASE_NAME}-${COUNTER}.md"
    COUNTER=$((COUNTER + 1))
done

# 3. Write note content into TARGET_PATH (using agent file tools or cat << 'EOF')

# 4. Commit and push
git -C "$TMP/repo" add "$FILE_NAME"
git -C "$TMP/repo" commit -m "notes: <kebab-case-topic> ($(date +%Y-%m-%d))"

# 5. Pull rebase to handle concurrent commits, then push
git -C "$TMP/repo" pull --rebase origin main || git -C "$TMP/repo" pull --rebase origin master || true
git -C "$TMP/repo" push origin HEAD
```

---

### Phase 4: Confirmation & Handoff

Report back to the user with the direct clickable GitHub URL:
```text
✅ Session notes successfully saved and published!
📄 File: https://github.com/<owner>/<repo>/blob/main/<filename>.md
```

---

## Safety & Security Rules

1. **Zero Secret Leakage:** Never push API tokens, SSH keys, private IPs, or internal confidential credentials.
2. **Never Force Push (`--force`):** Always use standard `git push origin HEAD` or rebase cleanly.
3. **Immutable History:** Never modify, overwrite, or delete existing notes or files in the remote repository. Only create new notes.
4. **Clean Workspaces:** Always perform git operations in an isolated `$TMP` directory, never polluting the user's active local workspace.
