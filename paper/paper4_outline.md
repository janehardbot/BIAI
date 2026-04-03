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
