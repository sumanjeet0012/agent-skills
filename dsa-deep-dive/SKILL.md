---
name: dsa-deep-dive
description: >
  Deep research a DSA (Data Structures & Algorithms) topic and produce an exhaustive, structured study guide:
  must-know core concepts, complexity tables, visual diagrams, verified top internet resources,
  crucial problem-solving patterns, beginner & advanced pitfalls, a practice ladder, and next-step paths.
  Use when the user asks to "research X for DSA", "study guide for X", "deep dive into <topic>",
  "explain <algorithm> thoroughly", "how does <data structure> work", or asks what to learn after mastering a topic.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment with web search tools"
license: MIT
---

# DSA Deep Dive Study Guide Generator

A comprehensive research and curriculum engine for mastering Data Structures and Algorithms. Transforms any DSA topic into a structured, production-grade master study guide blending theoretical rigor, visual intuition, and competitive interview readiness.

---

## Trigger Phrases

| User Input | Action Taken |
|---|---|
| "Research Dynamic Programming for DSA" | Full topic research & comprehensive study guide |
| "Deep dive into Graph algorithms (BFS, DFS, Dijkstra)" | Multi-algorithm comparison, state trees & practice plan |
| "Create a study guide for Monotonic Stack" | Invariant analysis, visual walkthrough & pattern identification |
| "What should I learn after Binary Search?" | Prerequisite graph, next-step roadmap & advanced applications |

---

## Execution Workflow

### Step 1: Scope & Context Detection
1. **Identify Topic Depth:**
   - If the topic is extremely broad (e.g., "Graphs" or "Trees"), propose a logical split into foundational vs advanced modules (e.g., Traversals vs Shortest Path vs Minimum Spanning Trees) and ask which to prioritize first, or deliver a high-level roadmap with a deep dive on the foundations.
2. **Detect Language Preference:**
   - Inspect workspace manifests (`pyproject.toml` -> Python, `go.mod` -> Go, `Cargo.toml` -> Rust, `pom.xml`/`build.gradle` -> Java, `package.json` -> TypeScript/JavaScript). If no workspace context exists, ask user preference or default to Python for readability with Java/C++ performance notes.

### Step 2: Live Research (Always Verify)
Gather authoritative, current references using web search and URL fetching tools:
- **Foundational References:** CLRS ("Introduction to Algorithms"), Sedgewick, CP-Algorithms (`cp-algorithms.com`), USACO Guide (`usaco.guide`).
- **Interactive & Visuals:** VisuAlgo (`visualgo.net`), Algorithm Visualizer, LeetCode Discuss articles.
- **Top Curated Problem Sets:** NeetCode 150, Blind 75, CSES Problem Set, Striver’s SDE Sheet.
- **Video & Explanations:** Abdul Bari, William Fiset, NeetCode, Take U Forward.
*(Verify URLs via search. If a URL cannot be verified live, output an exact search query string rather than fabricating a link).*

---

### Step 3: Produce the Master Study Guide

Generate the guide following this exact 9-section structure:

```markdown
# 📚 <Topic Name> — Deep Dive Study Guide

## 1. Executive Definition & Complexity Signature
- **Core Concept:** 2–3 sentences defining what it is and the fundamental problem class it solves.
- **Big-O Complexity Matrix:**

| Operation / Variant | Best Time | Average Time | Worst Time | Space (Auxiliary) | Invariant / Condition |
|---|---|---|---|---|---|
| Access / Search | $O(...)$ | $O(...)$ | $O(...)$ | $O(...)$ | E.g., sorted array |
| Insertion / Update | $O(...)$ | $O(...)$ | $O(...)$ | $O(...)$ | E.g., rebalancing needed |
| Deletion | $O(...)$ | $O(...)$ | $O(...)$ | $O(...)$ | E.g., heapify-down |

---

## 2. Visual Architecture & State Diagram
Provide a clean ASCII diagram or Mermaid flowchart illustrating the data structure memory layout or algorithm state transitions:

```text
[ASCII illustration showing pointers, tree nodes, memory layout, or recursion stack]
```
*(Or use a Mermaid diagram for state machines and tree / graph structures).*

---

## 3. Must-Know Concept Checklist
Grouped by mastery level. The user should be able to check each off sequentially:
- [ ] **Prerequisites:** Foundational building blocks (e.g., recursion, pointers, array indexing).
- [ ] **Core Mechanics:** Essential operations, invariants, and termination conditions.
- [ ] **Advanced Nuances:** Amortized analysis, space optimizations (e.g., 2D to 1D array reduction), edge-case guards.

---

## 4. Curated Learning Resources (Ranked with Rationale)
Provide 3–5 highest-signal resources with clear justification:

| Rank | Resource Name | Format (Article/Video/Interactive) | Best For | Verified Link or Query |
|---|---|---|---|---|
| 1 ⭐ | [Resource Title] | Interactive / Video | Visualizing state changes | `[Link]` or `search: "<exact query>"` |
| 2 | [Resource Title] | Article / Textbook | Deep mathematical proof | `[Link]` or `search: "<exact query>"` |

*(Explicitly state which ONE resource to begin with today).*

---

## 5. Canonical Implementation & Clean Template
Provide an idiomatic, reusable code template in the user's preferred language with comments on tricky boundaries:

```python
# Clean, commented template implementation
```

Include a brief step-by-step trace of the template on a tiny input (e.g., `input = [3, 1, 4]`).

---

## 6. Pattern Recognition & Problem-Solving Triggers
- **Trigger Heuristics:**
  - *"If the problem asks for shortest path in an unweighted grid → think BFS."*
  - *"If the problem requires finding elements satisfying a monotonic property in $O(\log N)$ → think Binary Search on Answer."*
  - *"If the problem asks for contiguous subarray maximum/minimum → think Sliding Window or Monotonic Queue."*
- **Decision Matrix:** A side-by-side comparison table for tricky choices (e.g., BFS vs DFS, Memoization vs Tabulation, Prim's vs Kruskal's).

---

## 7. Common Pitfalls & Edge Cases
Format each failure point as:
- ❌ **Beginner Misconception:** The flawed assumption.
  - ✅ **Reality & Fix:** Why it breaks and how to handle it.
  - 🔍 **Concrete Breaking Example:** Input that triggers the bug.
- ❌ **Advanced / Subtle Trap:** Off-by-one, integer overflow, recursion limit, memory explosion, or pointer aliasing.

---

## 8. Practice Ladder (Progressive Mastery)
Ranked problems with platform and difficulty:
1. **Foundation (Warm-up):** 2 problems to verify basic template mechanics.
2. **Intermediate (Interview Core):** 3–4 standard problems covering classic variations.
3. **Advanced (Twist / Multi-pattern):** 1–2 challenging problems combining multiple techniques.
*(Include problem numbers and direct search titles).*

---

## 9. Interview Readiness & Verbal Rehearsal
- **Typical Interview Framings:** 2–3 ways interviewers disguise this topic in open-ended problems.
- **The 60-Second Elevator Pitch:** The concise verbal explanation the candidate should rehearse when introducing their approach.
- **Follow-up Probing Questions:** 2 tough follow-up questions interviewers typically ask after the initial solution works (e.g., "What if data doesn't fit in memory?", "Can we do this in $O(1)$ space?").
```

---

## Quality Rules & Guidelines

1. **Precision in Complexity:** Always distinguish between Auxiliary Space and Total Space (including input). Never state time complexity without qualifying Best vs Worst case.
2. **Verify External Links:** Never hallucinate URLs. Perform a web search to confirm current links or provide explicit search queries.
3. **Scannable & Actionable:** Use tables, checklists, code blocks, and diagrams. Avoid unformatted walls of text.
4. **Interactive Handoff:** Conclude by offering:
   - To run a self-test quiz on the checklist.
   - To use `problem-intuition` on any specific problem in the practice ladder.
   - To generate a targeted practice list using `leetcode-question-finder`.
