---
name: leetcode-unstuck
description: >
  Socratic diagnostic coach when stuck on a LeetCode or competitive coding problem.
  Diagnoses failing code, Time Limit Exceeded (TLE), Memory Limit Exceeded (MLE), or incorrect outputs.
  Provides a 3-tier progressive hint system (Observational Counterexample -> Invariant Failure -> Minimal Diff Fix)
  to build true algorithmic intuition rather than spoiling the solution.
  Use when the user says "I'm stuck on problem X", "why is my LeetCode solution failing",
  "getting TLE on test case 42", "wrong answer on LeetCode", or pastes failing algorithm code.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment with shell/code execution"
license: MIT
---

# LeetCode Unstuck: Socratic Diagnostic Coach

When code fails sample tests or hidden test cases, this skill acts as an expert DSA interview coach. Instead of bluntly dumping a full replacement solution, it identifies the precise mechanical failure, presents a concrete breaking test case, and offers structured, progressive hints so the engineer learns how to debug algorithmic code under interview conditions.

---

## Trigger Phrases

| User Input | Diagnostic Mode |
|---|---|
| "Why is my code failing test case 14?" | Counterexample & variable trace diagnosis |
| "I'm getting Time Limit Exceeded (TLE) on LeetCode 322" | Complexity bottleneck & constraint mismatch analysis |
| "Getting Wrong Answer on LeetCode; here is my code" | Logic, off-by-one, or broken invariant diagnosis |
| "Help me get unstuck without giving me the answer" | Tiered progressive hints (Level 1 $\to$ Level 2) |

---

## Diagnostic Workflow

### Phase 1: Execution & Verification (Don't Guess — Test!)
If a code execution or shell tool is available:
1. Run the user's code against the provided sample inputs.
2. Run it against canonical edge cases:
   - Empty input (`""`, `[]`, `None`)
   - Single element (`[1]`)
   - All identical elements (`[2, 2, 2, 2]`)
   - Maximum constraint bounds ($N = 10^5$)
   - Negative values and zeroes
   - Inverted or already-sorted arrays
3. Note the exact difference between **Actual Output** vs **Expected Output**.

---

### Phase 2: Identify the Root Cause Category
Classify the failure into one of three distinct categories:
1. **Algorithmic / Complexity Mismatch ($O(2^N)$ or $O(N^2)$ vs required $O(N \log N)$):**
   - Symptoms: Time Limit Exceeded (TLE), Memory Limit Exceeded (MLE) on recursion stack.
   - Root Cause: Brute-force recursion without memoization, DFS where BFS shortest-path is required, nested linear searches where hash set / heap is needed.
2. **Broken Loop / Recursion Invariant:**
   - Symptoms: Wrong Answer on large inputs, premature termination, infinite loops.
   - Root Cause: Visited set checked too late (after enqueue instead of before enqueue), comparator returning wrong boolean order, greedy choice failing on non-matroid structure.
3. **Mechanical / Language Bug:**
   - Integer overflow (e.g., `(l + r) / 2` in C++/Java vs `l + (r - l) / 2`).
   - Object reference comparison (`==` instead of `.equals()` in Java, shallow copy issues in Python `[[]] * n`).
   - Modulo arithmetic with negative numbers (`(a % m + m) % m`).
   - Off-by-one in array slicing or loop boundaries (`<=` vs `<`).

---

## The 3-Tier Progressive Hint Structure

Format the coaching response so the user can control how much help they receive:

```markdown
# 🩺 Diagnosis: [Problem Name / Number]

### 1. The Core Diagnosis in One Sentence
> *"Your logic correctly handles standard cases, but fails because [specific invariant / constraint violation]."*

---

### 2. The Minimal Counterexample
Show the simplest concrete input where the code produces an incorrect result:
- **Input:** `nums = [1, 2], target = 3`
- **Expected Output:** `[0, 1]`
- **Your Code's Output:** `IndexOutOfBoundsException` (or `[-1, -1]`)
- **What Happened:** Trace the exact line where execution diverged from expectations.

---

### 3. Tiered Progressive Hints (Choose Your Level of Help)

#### 🟡 Hint 1: The Observation (No Code)
> [!NOTE]
> Look closely at how you handle [variable or condition]. Notice what happens when the input contains [edge case trait, e.g., duplicates or odd lengths].

#### 🟠 Hint 2: The Invariant & Location (Conceptual Fix)
> [!IMPORTANT]
> In lines [X–Y], you are updating [variable] before checking [condition]. The invariant requires that [state] must be verified before [action]. 
> *Question to consider: What should happen if `visited[node]` is already true when peering at a neighbor?*

#### 🔴 Hint 3: The Minimal Diff (Direct Code Remedy)
Only apply this if you are completely blocked or explicitly asked for the code fix:

```diff
- while left < right:
+ while left <= right:
-     mid = (left + right) // 2
+     mid = left + (right - left) // 2
```

---

### 4. 🧠 The "Next Time You See X, Think Y" Rule
A single, transferable mental heuristic to prevent this bug in future problems:
> *"Always mark a node as visited the exact moment you enqueue it in BFS, never when you dequeue it. Deferring to dequeue allows duplicate nodes to be enqueued exponentially."*

---

### 5. Check-In Question
*"Would you like to try fixing this now, or would you like to walk through a dry-run trace of the corrected loop together?"*
```

---

## Coaching Guidelines

1. **Never Rob the User of the "Aha!" Moment:** Unless the user explicitly demands the full code immediately, prioritize Hint 1 and Hint 2.
2. **Minimal Diffs Over Full Rewrites:** When presenting code, show only the surgical diff. A full code rewrite obscures the bug and discourages the user.
3. **Validate With Real Execution:** Always verify the bug with a concrete counterexample before making definitive claims.
