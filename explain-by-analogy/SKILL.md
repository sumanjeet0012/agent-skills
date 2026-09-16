---
name: explain-by-analogy
description: >
  Explain complex, confusing, or abstract technical concepts using vivid, relatable real-world analogies.
  Acts as the ultimate "Explain Like I'm 5" (ELI5) fallback when conventional documentation or standard
  explanations fail to click. Use when the user says "I don't get it", "explain like I'm 5", "ELI5",
  "give me an analogy", "dumb this down", "make a simpler comparison", or "it's not clicking".
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment"
license: MIT
---

# Explain By Analogy (ELI5 Technical Bridge)

When technical jargon and abstract diagrams fail to create understanding, this skill provides a visceral, real-world metaphor that sparks the intuitive *"Aha! Now I get it!"* moment, followed immediately by a direct mapping back to technical reality.

---

## Trigger Phrases

| User Input | Context & Intent |
|---|---|
| "Explain like I'm 5" / "ELI5" | Demands zero-jargon, intuitive physical metaphor |
| "I still don't understand how X works" | Conventional technical explanation failed; needs a fresh mental model |
| "Give me an analogy for Kubernetes / Kafka / Monads" | Needs a real-world system comparison |
| "Dumb this down for me" | Strip away mathematical and engineering jargon |
| "It's not clicking" | Reset mental model using common everyday experiences |

---

## Mental Model Selection Strategy

Before composing the response, analyze the mechanism silently:
1. **Isolate the Singular Core Invariant:**
   - Stack: Last In, First Out (LIFO).
   - Event Loop: Single-threaded worker checking an order queue while delegating long cook jobs.
   - Concurrency vs Parallelism: 1 chef multitasking between 2 stoves vs 2 chefs cooking simultaneously.
   - Asynchronous I/O: Receiving a restaurant buzzer pager and sitting down vs waiting rigidly at the pickup counter.
   - Cache / Redis: Keeping your most-used books on your nightstand instead of walking to the central library.
2. **Select Familiar, Universally Understood Domains:**
   - Restaurants, kitchens, and baristas.
   - Postal services, courier deliveries, and mailboxes.
   - Traffic intersections, toll booths, and airport security lines.
   - Libraries, bookshelves, and notebook binders.
   - Retail checkout queues and vending machines.
3. **Verify Mechanism Fidelity:**
   - *Anti-Pattern:* An analogy that shares superficial traits but breaks the mechanical invariant (e.g., "A database is like an Excel spreadsheet" misses concurrent locking, ACID transactions, and disk flushing).

---

## Output Structure

Deliver the response using this exact 5-part structure (keep total length under ~45 lines):

```markdown
# 💡 <Concept Name> — The Intuition

### 1. The Story (Pure Real-World Metaphor)
3–5 punchy sentences using strictly everyday scenarios. **Zero technical jargon permitted.** Make it visual, tactile, and immediately relatable.

### 2. The Rosetta Stone (Mapping Table)
Connect the story elements 1-to-1 with the technical components:

| In the Everyday Story... | ...in Technical Reality | What It Does |
|---|---|---|
| [Story element 1] | [Technical term 1] | [Plain English role] |
| [Story element 2] | [Technical term 2] | [Plain English role] |
| [Story element 3] | [Technical term 3] | [Plain English role] |

### 3. Where the Analogy Breaks Down
*Crucial: Real understanding happens at the seam where the physical metaphor stops matching software constraints.*
1–2 concise sentences clarifying the boundary (e.g., *"Unlike a physical restaurant buzzer, computer memory can handle 100,000 asynchronous tasks at once without physically running out of buzzers"*).

### 4. The Technical Anchor (3-Line Code / Command Reality)
A tiny snippet showing how the analogy translates into actual syntax:
```python
# The story translated into code in 2-3 lines
result = await fetch_data()  # The buzzer buzzes when ready
```

### 5. The One-Liner to Remember
A memorable sentence the user can quote during an interview or recite when debugging.

---

### 6. The "Did It Click?" Mini-Test
End with a single, fun, one-sentence check question based on the analogy to confirm comprehension:
> *"Quick check: If our restaurant runs out of pager buzzers and every customer must stand in a single line, did our system just become synchronous or concurrent?"*
```

---

## Rules of Engagement

1. **Absolute Jargon Ban in Section 1:** If a technical term must be referenced, translate it immediately in parentheses (e.g., *"the order ticket (the request payload)"*).
2. **One Strong Analogy Only:** Never provide three half-baked metaphors in one answer. Commit to one clear, cohesive story.
3. **Adaptive Domains:** If the user mentions working in music, finance, gaming, or logistics, tailor the analogy to their specific background.
4. **Never Lecture if Follow-up Fails:** If the user answers the mini-test incorrectly or says they still do not understand, **switch to an entirely different domain** (e.g., from restaurant cooking to postal delivery). Never repeat the same analogy louder or slower.
