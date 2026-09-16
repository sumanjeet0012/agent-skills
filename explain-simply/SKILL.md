---
name: explain-simply
description: >
  Explain any technical topic, system, protocol, or data structure in crystal-clear, structured terms
  tailored for software engineers, DevOps, and IT professionals. Blends plain-English intuition,
  architecture diagrams, concrete code or CLI examples, real-world trade-offs, and common pitfalls.
  Use when the user asks "what is X", "how does X work", "explain Kubernetes / Kafka / OAuth / Raft",
  or asks for a clear technical breakdown of any concept without academic fluff.
compatibility: "Antigravity, Claude Desktop, Cursor, Cowork, Codex — any agent environment"
license: MIT
---

# Explain Simply: Layered Technical Primer

Explains complex computing concepts, distributed systems, network protocols, and architectural patterns so clearly that an engineer can grasp the foundational mechanics in minutes, understand real-world trade-offs, and explain it confidently to someone else.

---

## Trigger Phrases

| User Input | Response Style |
|---|---|
| "What is Kubernetes / Kafka / OAuth2 / Raft?" | Full 8-layer structured primer |
| "How does TLS / DNS / BGP work under the hood?" | Protocol sequence diagram + concrete packet/command flow |
| "Explain Database Indexing / B-Trees simply" | Visual structure + disk I/O comparison + code walkthrough |
| "Explain X like an interviewer would want to hear" | Crisp 60-second summary + deep architectural trade-offs |

---

## The 8-Layer Explanation Architecture

Deliver explanations following this progressive structure:

```markdown
# 🧩 Understanding <Topic Name>

### 1. The 10-Second Elevator Pitch
A single plain-English sentence capturing the core function without buzzwords.
> *"A message queue like Kafka is an append-only log book where publishers record events and subscribers read them at their own pace without crashing each other."*

---

### 2. The Problem: Why Did We Need This?
Explain the historical or engineering pain point that forced this technology to exist:
- **Before <Topic>:** How systems were built previously and why they failed at scale or under stress.
- **The Breakthrough:** The exact paradigm shift or insight this concept introduced.

---

### 3. How It Works Under the Hood
Explain the mechanical internals step-by-step:
1. **Core Components:** Name the 2–4 essential moving parts (e.g., Producer, Broker, Partition, Consumer Group).
2. **The Lifecycle / Request Flow:** Trace what happens from initiation to completion.
3. **Key Guarantees & Constraints:** ACID properties, consistency models, delivery guarantees (at-most-once vs at-least-once).

---

### 4. Visual Architecture
Provide a clean ASCII diagram or Mermaid flowchart illustrating component interaction or data flow:

```text
[ASCII diagram showing client, proxy, server, storage, or message flow]
```
*(Or use a Mermaid `sequenceDiagram` or `graph TD` when displaying multi-node communications).*

---

### 5. Concrete Code or CLI Example
Never leave the explanation purely abstract. Provide runnable code or CLI verification:
- **For APIs / Systems:** `curl` command with expected response payload or SDK configuration.
- **For Protocols:** Step-by-step handshake trace or diagnostic command (`dig`, `traceroute`, `openssl s_client`).
- **For Algorithms / DSA:** Clean, commented snippet in the user's workspace language (default: Python or Go).

---

### 6. Where It's Used & Real-World Trade-Offs
| Strengths (When to Use) | Weaknesses / Costs (When NOT to Use) |
|---|---|
| High throughput / Decoupled scaling | Operational overhead / Complexity |
| Replayability of event streams | Not suited for random key updates |

*Real-world production examples (e.g., how Netflix, Stripe, or Uber apply this).*

---

### 7. The 3 Most Common Misconceptions
- ❌ **Myth 1:** Common false assumption.
  - ✅ **Truth:** The technical reality and why it matters in production.
- ❌ **Myth 2:** Misunderstood performance or security claim.
  - ✅ **Truth:** The actual constraint.

---

### 8. Interview Readiness & Self-Test
- **The 60-Second Interview Response:** How to summarize this concept cleanly when an interviewer asks "Can you explain how X works?"
- **Quick Self-Check Question:** 1 scenario-based question to verify mastery (e.g., *"If consumer group A lags behind, does it block consumer group B?"*).
```

---

## Operating Guidelines

1. **Progressive Precision:** Start with the simplest accurate mental model, then layer on technical precision. Never start with formal mathematical or RFC definitions.
2. **Language Adaptation:** Detect the user's language/framework from repository files (`package.json`, `go.mod`, `pyproject.toml`) and tailor code snippets accordingly.
3. **Scannable Depth:** Use bullet points, bold keywords, tables, and diagrams. Keep total length under ~150 lines.
4. **Scope Control for Massive Topics:** If a user asks to explain a massive ecosystem (e.g., "Explain AWS" or "Explain Linux"), give the high-level architecture map first and invite them to pick specific subsystems (e.g., compute, networking, storage).
