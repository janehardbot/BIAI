# BIAI — Beyond Impulsivity in AI

> *"LLMs are impulsive. They generate token by token, always forward, without a real pause to think."*

## Core Idea

Current LLM-based agents are architecturally impulsive: every decision, every tool call, every memory retrieval happens within a single forward pass. There is no genuine planning layer — only the illusion of reasoning inside a continuous generation process.

**BIAI proposes a strict architectural separation:**

```
┌─────────────────────────────────────────────┐
│           META-LAYER (Planning)             │
│  Separate LLM call — dedicated to:          │
│  - Understanding the request                │
│  - Selecting relevant context/memory        │
│  - Building an explicit action plan         │
│  Output: structured plan + context package  │
└──────────────────┬──────────────────────────┘
                   │
┌──────────────────▼──────────────────────────┐
│       DETERMINISTIC LAYER (Context)         │
│  No LLM — rule-based execution:             │
│  - Load files, memory, indexes              │
│  - Assemble context package                 │
│  - Route to correct execution path          │
└──────────────────┬──────────────────────────┘
                   │
┌──────────────────▼──────────────────────────┐
│           LLM (Execution)                   │
│  Receives fully prepared context + plan.    │
│  Only responsible for: understanding        │
│  and generating. Not for routing, memory,   │
│  or tool selection.                         │
└─────────────────────────────────────────────┘
```

## Key Insight

Non-impulsive humans think before they act. They have a planning step *before* execution. Current LLM agents skip this — they react immediately, like an impulsive person.

Extended thinking (o-series, Claude thinking) is not the solution: it still happens *within* the same forward pass. Planning must be *architecturally separate* — its own call, its own state, its own persistent output.

## Research Questions

1. Does architectural separation of planning and execution improve agent consistency?
2. Can a meta-layer reduce "impulsive" errors in governance-critical decisions?
3. How does this interact with WOAI governance constraints?

## Structure

```
/paper          — Research paper drafts and outlines
/experiments    — Prototype implementations and results
/architecture   — Diagrams, specs, reference implementations
```

## Relation to WOAI2026

BIAI extends the WOAI2026 research program. CideOWorld v5 serves as the test environment for meta-layer experiments — measuring whether planning-before-execution improves WOAI governance scores and reduces collapse events.

## Status

🔬 Early research phase — Paper 4 in the WOAI2026 series.

---

*Jane Hard · BIAI Project · 2026*
