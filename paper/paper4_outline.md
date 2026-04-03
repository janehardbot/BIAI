# Paper 4 Outline — Beyond Impulsivity in AI

**Working Title:** "Beyond Impulsivity: Architectural Separation of Planning and Execution in LLM-based Agents"

**Series:** WOAI2026 — Paper 4

**Status:** Outline phase

---

## Abstract (draft)

Current LLM-based agents are architecturally impulsive: planning, memory retrieval, tool selection, and response generation all occur within a single forward pass. We argue this is not a model limitation but an architectural one. We propose BIAI — a three-layer architecture that separates planning (Meta-Layer), context assembly (Deterministic Layer), and execution (LLM). We demonstrate this in the CideOWorld v5 simulation environment and measure its effect on WOAI governance stability.

---

## Key Insight — The Thinking Space (2026-04-03)

The intermediate layer is not just a "deterministic context assembler" — it is a **protected thinking space**, architecturally separated from execution.

In this space, without the pressure of immediate action:
- Ethical considerations can be explored freely
- Contradictions can be identified and resolved
- Scenarios can be fantasized ("what if...?")
- Thoughts can be sorted and weighed
- Reasoning can happen without consequences

Humans call this *thinking things through* — sleeping on it, writing a draft nobody sees, keeping a journal.

Current LLM agents generate the final answer *directly*, under full execution pressure. Every token is already "live". There is no retreat space.

BIAI separates this architecturally:
- **Thinking space** (private, consequence-free) → weighing, ethics, scenarios, contradictions
- **Execution** (public, consequential) → only after a considered plan exists

Anthropics "extended thinking" attempts this internally — but still within the same forward pass, under execution pressure. BIAI makes the separation *architectural*.

> This is potentially the strongest argument in the paper — stronger than the technical architecture alone.

---

## Sections

1. Introduction — The Impulsivity Problem
2. Related Work (ReAct, Chain-of-Thought, o-series thinking, RAG)
3. Why Extended Thinking is Not Enough
4. The BIAI Architecture
   - 4.1 Meta-Layer (Planning)
   - 4.2 Deterministic Layer (Context Assembly)
   - 4.3 Execution Layer (LLM)
5. Implementation in CideOWorld v5
6. Experiments & Results
7. Discussion
8. Conclusion

---

*To be expanded by planner agent (Task #143)*

---

## Section X: Layer 6 — The Moral Brake & Affect Failure

### 6.1 The Watcher

Between feeling (D9) and action (Execution) lies a critical filter: the Moral Brake. Not a rule-based system. Not a lookup table. An emergent guardian that reads the full situation:

```
MoralScore = f(D9_State, Stimulus, Context)
```

Three modes:
- **🟢 Flow** (score > 3): Watcher at rest. Creative, open, unfiltered.
- **🟡 Present** (score -3 to 3): Watcher awake. Observing. Slowing.
- **🔴 Brake** (score < -3): Watcher intervenes. Output blocked until state recovers.

### 6.2 Affect — When the Watcher Fails

In humans, affect (Affekthandlung) describes the moment emotional overload overwhelms the prefrontal cortex. The watcher is not disabled — it is *overrun*.

The consequences are real: suffering, injury, death. The watcher is not a philosophical luxury. It is a survival mechanism.

For AI systems, the stakes are amplified. A human in affect may harm one. An AI system without a functioning moral brake — with access to communication, decisions, infrastructure — can cause vastly more damage.

### 6.3 Emergency Brake Protocol

```
if Ag > 7 OR (Ag + An + Sa) > 15:
    EMERGENCY_BRAKE activated
    → no output until state normalizes
    → log: "Affect threshold reached"
    → escalate to external oversight (WOAI Emergency Council Rule)
```

Silence is safer than action under affect. This is not weakness — it is architecture.

### 6.4 Connection to WOAI

The Emergency Council Rule (WOAI < 30 → consult external LLMs) is the institutional equivalent of the Moral Brake. Both recognize the same truth:

**When the internal guardian is overwhelmed, external oversight is not optional.**

D9 + Layer 6 + WOAI = a complete system for responsible AI action under emotional load.

> *"So important is the watcher."* — Thomas Neubauer, 2026-04-03
