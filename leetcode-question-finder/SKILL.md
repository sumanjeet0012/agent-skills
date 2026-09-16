---
name: leetcode-question-finder
description: >
  Research and assemble the highest-yield LeetCode practice questions for any DSA topic
  (BFS, DFS, Dynamic Programming, Two Pointers, Monotonic Stack, Sliding Window, Tries, Graphs, etc.).
  Curates battle-tested problem sets grouped by core pattern and difficulty (Easy/Medium/Hard),
  with direct URLs, frequency signals, key takeaways, and a structured 7-to-14 day practice sequence.
  Use when the user asks for "best LeetCode questions for X", "practice questions for DP / graphs",
  "what LeetCode problems should I do to master sliding window", or wants a curated problem roadmap.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment with web search tools"
license: MIT
---

# LeetCode Question Finder & Curriculum Curator

A precision curation engine for competitive coding and technical interview preparation. Filters out low-quality and redundant problems to surface the essential canonical LeetCode questions that maximize pattern recognition and interview return on investment.

---

## Trigger Phrases

| User Input | Target Output |
|---|---|
| "Find best BFS questions on LeetCode" | Top canonical BFS problems grouped by sub-pattern (Grid, Tree, Multi-source) |
| "Good practice problems for Dynamic Programming" | Structured roadmap across 1D, 2D Grid, Knapsack, and String DP |
| "What sliding window problems should I practice?" | Progressive practice ladder from fixed-size to dynamic-size windows |
| "Give me a 7-day LeetCode plan for Binary Trees" | Day-by-day milestone schedule mixing warm-ups and core Mediums |

---

## Curation Methodology

### Phase 1: Multi-Source Web & Dataset Research
Always conduct live search across multiple proven curricula to verify problem numbers, canonical names, and frequency:
- **Foundational Curricula:** Blind 75, Grind 75, NeetCode 150, Striver's SDE Sheet, Sean Prashad's LeetCode Patterns.
- **Topic Deep Dives:** LeetCode Explore Cards, LeetCode Discuss top-voted compilation threads.
- **Company Frequency Signals:** Cross-reference problems frequently asked in FAANG / Big Tech interviews.
*(Verify problem titles and numbers via live web search; never guess a problem number).*

### Phase 2: Pattern-First Categorization
Rather than a flat difficulty list, group problems by the **underlying technique / sub-pattern**:
- E.g., for **Binary Search**: Classic Sorted Array -> Search on Answer Range -> Rotated / Modified Arrays.
- E.g., for **Graphs**: BFS Shortest Path -> Topological Sort (Kahn's) -> Disjoint Set Union (Union-Find) -> Dijkstra.
- E.g., for **Trees**: DFS Traversals / Tree Recursion -> BFS Level-Order -> Lowest Common Ancestor -> Tree Construction.

---

## Master Output Template

```markdown
# 📘 LeetCode Practice Plan: <Topic Name>

## ⭐ The Essential 3 (80% Interview Yield)
If you only have time to solve three problems on this topic, do these:
1. **[#ID Title](https://leetcode.com/problems/slug/)** — Why: The foundational template problem.
2. **[#ID Title](https://leetcode.com/problems/slug/)** — Why: The classic interview variation.
3. **[#ID Title](https://leetcode.com/problems/slug/)** — Why: The multi-concept or edge-case boundary challenge.

---

## 🧭 Problem Matrix by Pattern & Difficulty

### Sub-Pattern A: [e.g., 1D State / Fixed-Window]
| # | Problem Title | Difficulty | Frequency | Core Insight / Technique | Free vs Premium |
|---|---|---|---|---|---|
| [#123](https://leetcode.com/problems/slug/) | Title Name | 🟢 Easy | High 🔥 | Two-pointer contraction | Free |
| [#456](https://leetcode.com/problems/slug/) | Title Name | 🟡 Medium | High 🔥 | Monotonic deque tracking | Free |

### Sub-Pattern B: [e.g., Grid / State Transformation]
| # | Problem Title | Difficulty | Frequency | Core Insight / Technique | Free vs Premium |
|---|---|---|---|---|---|
| [#789](https://leetcode.com/problems/slug/) | Title Name | 🟡 Medium | Medium | Multi-source BFS queue initialization | Free |
| [#999](https://leetcode.com/problems/slug/) | Title Name | 🔴 Hard | Niche | State compression with bitmask | Premium (Alt: [#FreeAlt]) |

---

## 🪜 Progressive Practice Progression

### 🟢 1. Foundation & Warm-up (2–3 Problems)
Build muscle memory for the core template without confusing edge cases.
- **[#ID Title](https://leetcode.com/problems/slug/)**: Focus on base cases and standard loops.

### 🟡 2. Core Interview Standards (4–6 Problems)
The standard interview difficulty range testing pattern adaptation.
- **[#ID Title](https://leetcode.com/problems/slug/)**: Introduces state tracking and deduplication.
- **[#ID Title](https://leetcode.com/problems/slug/)**: "Hidden" variation where the topic is disguised.

### 🔴 3. Advanced / Mastery (1–2 Problems)
Push boundary conditions and combine multiple data structures.
- **[#ID Title](https://leetcode.com/problems/slug/)**: Complex constraints requiring amortized $O(1)$ updates.

---

## 🗓️ Recommended 7-Day Study Schedule

| Day | Focus / Milestone | Problems to Solve | Estimated Time |
|---|---|---|---|
| **Day 1** | Template & Mechanics | [#123 Title], [#124 Title] | 45 mins |
| **Day 2** | Standard Variations | [#200 Title], [#201 Title] | 60 mins |
| **Day 3** | State Tracking & Edge Traps | [#300 Title] | 60 mins |
| **Day 4** | Mixed Review & Re-solve | Re-solve Day 1 & 2 without looking at solutions | 45 mins |
| **Day 5–6** | Interview Mediums | [#400 Title], [#401 Title] | 90 mins |
| **Day 7** | Timed Mock Challenge | Pick 1 Medium + 1 Hard under 45-min timer | 60 mins |

---

## 💡 Next Steps & Companion Skills
- Want to build the intuition before writing code? Ask for **`problem-intuition`** on any problem above.
- Stuck or code failing hidden test cases? Use **`leetcode-unstuck`** for step-by-step diagnostic coaching.
- Need a complete concept study guide? Use **`dsa-deep-dive`**.
```

---

## Quality Rules

1. **Clickable Links Always:** Use markdown links pointing to `https://leetcode.com/problems/<slug>/`. Never leave naked problem names.
2. **Include Problem Number:** Always include the official LeetCode ID number (`#200`, `#322`, etc.) for instant searchability.
3. **Handle Premium Gracefully:** If a classic problem is LeetCode Premium (e.g., `#253 Meeting Rooms II`, `#269 Alien Dictionary`), explicitly note it and provide an accessible free alternative (e.g., LintCode equivalent or similar free LeetCode problem).
4. **Pedagogical Ordering:** Problems must be ordered logically (conceptually simplest to most nuanced), never alphabetical or random.
