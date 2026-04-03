# BIAI Architecture Spec

## Three-Layer Model

### Layer 1: Meta-Layer (Planning)
- **Type:** Separate LLM call
- **Input:** Raw user request
- **Responsibility:**
  - Understand intent
  - Identify required context (which files, memory, history)
  - Build explicit action plan
- **Output:** Structured plan + context requirements (JSON)
- **Key property:** Runs *before* any execution — dedicated thinking step

### Layer 2: Deterministic Layer (Context Assembly)
- **Type:** No LLM — rule-based / OS functions
- **Input:** Plan from Meta-Layer
- **Responsibility:**
  - Load specified files and memory
  - Assemble context package
  - Route to correct execution path
- **Output:** Full context package for LLM
- **Key property:** Deterministic, reproducible, fast

### Layer 3: Execution Layer (LLM)
- **Type:** LLM call
- **Input:** Full context package + action plan
- **Responsibility:**
  - Understand (prediction over prepared context)
  - Generate response / decision
- **Output:** Final response or action
- **Key property:** Receives complete context — no impulsive tool calls

---

## Contrast with Current Architecture

| | Current (Impulsive) | BIAI |
|---|---|---|
| Planning | Implicit, within generation | Explicit, separate call |
| Memory | LLM decides impulsively | Deterministic Layer loads |
| Tool calls | Reactive, mid-generation | Pre-planned in Meta-Layer |
| Context | Whatever fits in window | Curated, structured package |
| Reproducibility | Low | High |

---

## Integration with CideOWorld v5

- Meta-Layer: pre-call before `/decide` — dedicated planning prompt
- Deterministic Layer: context builder (state, trends, critical params)
- Execution Layer: existing Bridge call with enriched payload

Experiment: compare WOAI scores with/without Meta-Layer active.
