---
name: problem-intuition
description: >
  Build the deep architectural and mathematical intuition for a LeetCode or DSA problem BEFORE writing any code.
  Analyzes problem constraints, mathematical bounds, input/output structures, and linguistic clues to map the
  problem directly to classic DSA patterns. Produces a pseudocode strategy, loop invariants, and a manual trace
  WITHOUT revealing the full completed solution. Use when the user asks "help me understand this problem",
  "how should I approach problem X", "build intuition for this problem", "what pattern does this map to",
  or pastes a problem description/link asking how to think about it.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment"
license: MIT
---

# Problem Intuition & Pre-Coding Strategy

A pre-coding algorithmic mentor that deconstructs LeetCode problems into first principles. Rather than spoiling the solution with copy-paste code, this skill equips the engineer with the mathematical bounds, pattern recognition triggers, and structural skeleton needed to implement the solution themselves.

---

## Trigger Phrases

| User Input | Core Objective |
|---|---|
| "How should I approach LeetCode 300?" | Constraint analysis, pattern deduction & skeleton approach |
| "I don't understand what this problem is asking" | Problem restatement in 1 plain sentence & worked manual example |
| "Build intuition for this problem without giving code" | Deduce invariants, state transitions & constraint-to-complexity map |
| "What algorithmic pattern applies here?" | Linguistic clues & mathematical reduction to canonical patterns |

---

## The 4-Step Intuition Framework

### Step 1: Constraint-to-Complexity Deduction (The Physics of the Problem)
Examine input constraints first. They dictate the maximum allowable time complexity:

| Constraint Size ($N$) | Maximum Allowed Big-O | Likely Algorithmic Techniques |
|---|---|---|
| $N \le 10$ | $O(N!)$ or $O(N^2 \cdot 2^N)$ | Backtracking, Permutations, Travelling Salesperson |
| $N \le 20$ | $O(2^N)$ or $O(2^N \cdot N)$ | Bitmask DP, Subset Generation, Meet in the Middle |
| $N \le 100$ | $O(N^4)$ or $O(N^3)$ | 2D/3D DP, Floyd-Warshall, Matrix Operations |
| $N \le 500$ | $O(N^3)$ | All-Pairs Shortest Path, Range/Interval DP |
| $N \le 5,000$ | $O(N^2)$ | 2D Dynamic Programming, Nested Loops, Pairwise Checks |
| $N \le 2 \times 10^5$ | $O(N \log N)$ or $O(N)$ | Sorting, Heaps, Binary Search, Segment Trees, Divide & Conquer |
| $N \le 10^6$ | $O(N)$ | Two Pointers, Sliding Window, Prefix Sum, Monotonic Stack, Hash Maps |
| $N \ge 10^9$ | $O(\log N)$ or $O(1)$ | Binary Search on Answer, Matrix Exponentiation, Math, Bit Tricks |

---

### Step 2: Linguistic Clue & Pattern Mapper
Match keywords in the problem description to structural patterns:

- *"Shortest path / minimum moves in unweighted matrix / graph"* $\implies$ **Breadth-First Search (BFS)** (guarantees shortest path in unweighted edges).
- *"Contiguous subarray sum equals K"* $\implies$ **Prefix Sum + Hash Map** (transforms $O(N^2)$ search to $O(1)$ lookup via $prefix[j] - prefix[i] = K$).
- *"Find maximum/minimum within window of size K"* $\implies$ **Sliding Window + Monotonic Deque** (maintains monotonic decreasing order).
- *"Next greater element / Stock span / Histogram area"* $\implies$ **Monotonic Stack** (resolves nearest smaller/larger neighbor in $O(N)$ total).
- *"Minimize the maximum / Maximize the minimum"* $\implies$ **Binary Search on the Answer** (monotonic feasibility check).
- *"Overlapping subproblems + optimal substructure"* $\implies$ **Dynamic Programming** (define sub-state $dp[i]$ and state transition equation).
- *"Top K frequent / Kth largest element"* $\implies$ **Min-Heap of size K** or **QuickSelect** ($O(N)$ average).
- *"Disjoint sets / dynamic connectivity / cycle detection in undirected graph"* $\implies$ **Union-Find (Disjoint Set Union)** with path compression.

---

## Output Template

Deliver the intuition breakdown using this exact structured format:

```markdown
# 💡 Problem Intuition: [#ID Title Name]

### 1. Plain-English Problem Restatement
> *"In plain words: Find the [target goal] such that [condition], where the tricky part is [core constraint or gotcha]."*

### 2. Constraints & Complexity Ceiling
- **Given Constraints:** E.g., $N \le 10^5$, elements between $-10^4$ and $10^4$.
- **Complexity Ceiling:** $O(N^2)$ will produce Time Limit Exceeded ($10^{10}$ operations vs standard $10^8$ ops/sec limit). Target must be $O(N \log N)$ or $O(N)$.
- **Space Ceiling:** $O(1)$ or $O(N)$ auxiliary storage permissible.

### 3. The "Aha!" Pattern Deduction
- **The Core Pattern:** Name the pattern (e.g., *Two Pointers on Sorted Array*).
- **The Why:** Why does this specific pattern work? Explain the invariant that eliminates redundant work (e.g., *"Because the array is sorted, if `nums[left] + nums[right] > target`, incrementing `left` will only make the sum larger; therefore, decrementing `right` is the ONLY valid search direction"*).

### 4. Step-by-Step Strategy (Pseudocode Skeleton)
Provide high-level procedural pseudocode with invariants (NO copy-paste full code):

1. **Initialization:** What data structures or pointers are initialized?
2. **Loop Invariant:** What condition is guaranteed to remain true on every iteration?
3. **State Transition / Choice:** If condition A holds, do X; otherwise do Y.
4. **Termination & Return:** When does the process conclude, and what is the final return value?

### 5. Manual Trace on a Tiny Input
Walk through 3–4 iterations on a small test case (e.g., `nums = [2, 7, 11, 15]`, `target = 9`):

| Iteration | State (Pointers / Queue / DP Table) | Current Evaluation | Next Action |
|---|---|---|---|
| 0 | `left = 0 (2)`, `right = 3 (15)` | `sum = 17 > 9` | Move `right` inward |
| 1 | `left = 0 (2)`, `right = 2 (11)` | `sum = 13 > 9` | Move `right` inward |
| 2 | `left = 0 (2)`, `right = 1 (7)` | `sum = 9 == 9` | Target found! Return indices |

### 6. Gotchas & Edge Cases to Handle in Code
- [ ] Empty / single-element input
- [ ] Duplicates / identical numbers
- [ ] Negative values or zero
- [ ] Potential integer overflow when adding large values

---

### 7. Over to You!
Now that the skeleton and invariants are clear:
- Try implementing this in your preferred language!
- If your code runs into bugs or passes sample cases but fails hidden tests, invoke **`leetcode-unstuck`** for diagnostic coaching.
```

---

## Strict Behavioral Rules

1. **NEVER Provide the Full Code Solution:** Provide only conceptual logic, loop invariants, and pseudocode skeletons. The entire pedagogical value comes from the user implementing the syntax themselves.
2. **Always Reason From Constraints First:** Begin algorithmic justification from computational complexity limits ($10^8$ operations per second limit).
3. **Interactive Engagement:** End with a direct checkpoint question or invitation for the user to write the code.
