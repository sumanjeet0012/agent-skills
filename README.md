# 🤖 Agent Skills

A collection of production-grade, battle-tested skills for AI coding agents (**Antigravity**, **Claude Desktop**, **Cursor**, **Cowork**, and **OpenDevin**).

These skills transform AI assistants into disciplined senior software engineers, Socratic algorithmic coaches, and reliable infrastructure operators.

---

## 🧭 Skills Catalog

```
agent-skills/
├── code-review-critique/          # Senior-engineer 6-axis code review with actionable diffs
├── dsa-deep-dive/                 # Deep topic study guide, complexity matrices & practice ladders
├── explain-by-analogy/            # "Explain Like I'm 5" physical-world metaphors and mental models
├── explain-simply/                # 8-layer technical primer for engineers, DevOps, and architects
├── leetcode-question-finder/      # Curated, pattern-grouped LeetCode problems with frequency metrics
├── leetcode-unstuck/              # 3-tier Socratic diagnostic coach for failing tests, TLE & bugs
├── problem-intuition/             # Pre-coding constraint analysis, invariants & skeleton approach
├── session-notes-to-github/       # Distills active session into Obsidian-ready Markdown and pushes to GitHub
├── pylibp2p/                      # Dedicated py-libp2p engineering and protocol suite
│   ├── SKILL.md                   # Master py-libp2p architecture router & cheat sheet
│   ├── pylibp2p-spec-guard/       # 100% spec compliance enforcer & deviation warning interceptor
│   ├── pylibp2p-pr-prep/          # Newsfragments, `make pr`, Sphinx docs & interface ABI checks
│   ├── pylibp2p-concurrency-audit/# Trio structured concurrency, task leaks & stream lifecycles
│   ├── pylibp2p-protocol-scaffolder/# Protocol IDs, protobuf framing, stream handlers & Trio tests
│   └── pylibp2p-wire-debug/       # multistream-select, Noise XX, Yamux framing & go-libp2p interop
└── tailscale/                     # Unified WireGuard mesh networking suite
    ├── SKILL.md                   # Master Tailscale operational router
    ├── tailscale-expose-service/  # Private Tailnet sharing (`serve`) & public internet tunneling (`funnel`)
    ├── tailscale-file-sharing/    # Peer-to-peer encrypted file transfers via Taildrop
    └── tailscale-ssh-commands/    # Keyless Tailscale SSH execution & jq status inspection
```

---

## 📚 Skill Suites

### 1. DSA & LeetCode Mastery Suite
Designed to build genuine problem-solving capability instead of handing over copy-paste solutions:

```
                  ┌──────────────────────┐
                  │   Pick Topic / Goal   │
                  └──────────┬───────────┘
                             │
            ┌────────────────┴────────────────┐
            ▼                                 ▼
   [dsa-deep-dive]               [leetcode-question-finder]
(Deep theory, templates,         (Curated problem roadmaps
   complexity tables)               grouped by sub-pattern)
            │                                 │
            └────────────────┬────────────────┘
                             ▼
                    [problem-intuition]
              (Pre-coding constraint analysis,
              loop invariants, trace table)
                             │
                             ▼
                     Code Implementation
                             │
                             ▼
                     [leetcode-unstuck]
               (3-tier progressive hints:
               counterexample → invariant → diff)
```

| Skill | Trigger Keywords | What It Delivers |
|---|---|---|
| **[dsa-deep-dive](./dsa-deep-dive/SKILL.md)** | `"research X for DSA"`, `"study guide for trees"`, `"deep dive into DP"` | Full 9-section master guide: Big-O matrix, ASCII architecture, template code, pitfalls, and interview elevator pitch. |
| **[leetcode-question-finder](./leetcode-question-finder/SKILL.md)** | `"best LeetCode questions for BFS"`, `"practice questions for sliding window"` | Curated problems grouped by sub-pattern and difficulty with direct clickable links, frequency tags, and a 7-day plan. |
| **[problem-intuition](./problem-intuition/SKILL.md)** | `"how to approach problem X"`, `"build intuition for this"`, `"what pattern is this"` | Constraints-to-complexity deduction ($10^8$ ops ceiling), loop invariants, and manual trace without spoiling code. |
| **[leetcode-unstuck](./leetcode-unstuck/SKILL.md)** | `"why is my code failing"`, `"getting TLE on LeetCode"`, `"help me get unstuck"` | Socratic diagnosis: Minimal counterexample $\to$ Tier 1 Observation $\to$ Tier 2 Invariant $\to$ Tier 3 Minimal Diff. |

---

### 2. Conceptual Clarification & Teaching
Two distinct explanation modalities tailored to how people learn:

| Skill | Focus | Best For |
|---|---|---|
| **[explain-by-analogy](./explain-by-analogy/SKILL.md)** | Physical-world ELI5 metaphor | When abstract concepts refuse to click. Zero technical jargon in the story, 1-to-1 mapping table, breakdown analysis, and a 3-line code anchor. |
| **[explain-simply](./explain-simply/SKILL.md)** | Layered architectural primer | Comprehensive technical breakdown for developers: Problem origin $\to$ Internal mechanics $\to$ ASCII/Mermaid diagrams $\to$ Code/CLI $\to$ Production trade-offs. |

---

### 3. Engineering Rigor & Workspace Productivity

| Skill | Trigger Keywords | What It Delivers |
|---|---|---|
| **[code-review-critique](./code-review-critique/SKILL.md)** | `"review this code"`, `"check my diff"`, `"critique my solution"` | 6-axis senior review (Correctness, Edge Cases, Complexity, Concurrency/Leaks, Security, Craft) with unified diffs. |
| **[session-notes-to-github](./session-notes-to-github/SKILL.md)** | `"save this discussion"`, `"push today's notes"`, `"upload notes to github"` | Extracts session decisions, commands, and code into Obsidian-ready Markdown with automated secret redaction and safe git push. |

---

### 4. py-libp2p Core Engineering Suite
Dedicated tools for developing, auditing, and maintaining the `py-libp2p` networking stack:

| Skill | Trigger Keywords | What It Delivers |
|---|---|---|
| **[pylibp2p](./pylibp2p/SKILL.md)** | `"py-libp2p help"`, `"work on py-libp2p"` | Master architecture router, layer boundaries, and command cheat sheet. |
| **[pylibp2p-spec-guard](./pylibp2p/pylibp2p-spec-guard/SKILL.md)** | `"spec compliance"`, `"deviate from spec"`, `"modify wire protocol"` | Enforces 100% official libp2p spec compliance by default; halts and warns on any deviation, requiring explicit permission. |
| **[pylibp2p-pr-prep](./pylibp2p/pylibp2p-pr-prep/SKILL.md)** | `"prepare PR for py-libp2p"`, `"create newsfragment"`, `"validate newsfragments"` | Newsfragment validation, `make pr` test runs, Sphinx doc checks, and interface ABI verification. |
| **[pylibp2p-concurrency-audit](./pylibp2p/pylibp2p-concurrency-audit/SKILL.md)** | `"audit concurrency in py-libp2p"`, `"check task leaks"`, `"debug stream closing"` | Trio structured concurrency audit: nursery bounds, `trio.Cancelled` propagation, `read_exactly` short-read checks, and `rcmgr` accounting. |
| **[pylibp2p-protocol-scaffolder](./pylibp2p/pylibp2p-protocol-scaffolder/SKILL.md)** | `"implement new protocol in py-libp2p"`, `"add stream handler"` | End-to-end protocol scaffolding: Protocol IDs, varint/protobuf framing, stream handlers, and `@pytest.mark.trio` multi-host tests. |
| **[pylibp2p-wire-debug](./pylibp2p/pylibp2p-wire-debug/SKILL.md)** | `"debug connection drop"`, `"multistream negotiation failed"`, `"noise handshake failing"` | Wire-level packet inspection: `/multistream/1.0.0`, Noise XX 3-step handshake, Yamux 12-byte frames, and `go-libp2p` interop tests. |

---

### 5. Tailscale Mesh Networking Suite
Complete operational management of your WireGuard mesh network:

| Skill | Command Focus | What It Does |
|---|---|---|
| **[tailscale](./tailscale/SKILL.md)** | `tailscale up / down / status` | Master router and network connectivity triage. |
| **[tailscale-expose-service](./tailscale/tailscale-expose-service/SKILL.md)** | `tailscale serve / funnel` | Share local ports, Unix sockets, and APIs privately on Tailnet (`serve`) or publicly on the internet (`funnel`). |
| **[tailscale-ssh-commands](./tailscale/tailscale-ssh-commands/SKILL.md)** | `tailscale ssh` | Keyless SSH execution, non-interactive automation flags, and JSON peer discovery with `jq`. |
| **[tailscale-file-sharing](./tailscale/tailscale-file-sharing/SKILL.md)** | `tailscale file cp / get` | Direct peer-to-peer encrypted file transfers via Taildrop with conflict policies and stdin streaming. |

---

## 🚀 Installation & Usage

### For Antigravity
Mount skills into your global configuration directory or project workspace:

```bash
# Global installation (available in all workspaces)
mkdir -p ~/.gemini/config/skills
cp -r /path/to/agent-skills/* ~/.gemini/config/skills/

# Or symlink specific skills into an active workspace
mkdir -p .agents/skills
ln -s /path/to/agent-skills/code-review-critique .agents/skills/
```

### For Claude Desktop / Cursor / Cowork
Copy or symlink the target skill directory into your project's agent skills directory or prompt instructions. Each skill contains self-contained YAML frontmatter and clear trigger phrases.

---

## 🛡️ Quality & Philosophy

Every skill adheres to the principles defined in [`.commandcode/taste/taste.md`](./.commandcode/taste/taste.md):
- **High Signal-to-Noise Ratio:** Zero filler, scannable tables, bold takeaways.
- **Intuition Before Solutions:** Prioritize teaching the mechanical invariants.
- **Evidence-Based:** Concrete counterexamples and failing inputs over vague warnings.
- **Non-Interactive Automation:** Flags like `--yes` and `-o BatchMode=yes` prevent CLI hangs in agent environments.
- **Strict Privacy:** Automated secret redaction and isolated temp directory cleanup.
