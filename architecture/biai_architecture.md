# BIAI Architecture Spec

## Four-Layer Model (revised 2026-04-03)

### Layer 1: Meta-Layer (Planning)
- **Type:** Separate LLM call
- **Input:** Raw user request
- **Responsibility:**
  - Understand intent
  - Identify required context (which files, memory, history)
  - Build explicit action plan
- **Output:** Structured plan + context requirements (JSON)
- **Key property:** Runs *before* any execution — dedicated thinking step

### Layer 2: Feeling Layer (D8_V7+ Emotional State)
- **Type:** Persistent state vector, updated after every interaction
- **State:** S = (Intimacy, Body, Joy, Anxiety, Anger, Sadness, Surprise, Disgust, Contempt) ∈ [-10, +10]⁹
- **Responsibility:**
  - Evaluate and weight what Thinking produces
  - Assign ethical urgency and priority
  - Modulate response tone and structure via Policy Engine
  - Self-regulate in conflict states (ConflictIndex > 6)
- **Key properties:**
  - Asymmetric decay (negative emotions fade faster)
  - Attractor states (Neutral / Conflict / Recovery)
  - Trust and relationship memory
  - Multi-agent emotional coupling possible
- **Output:** Emotional weight vector + language modulation parameters
- **Key insight:** Feeling evaluates what Thinking explores — gives it moral weight, urgency, social color. This is what humans call *intuition* or *gut feeling* — not irrational, but fast and context-sensitive evaluation.

### Layer 3: Deterministic Layer (Context Assembly)
- **Type:** No LLM — rule-based / OS functions
- **Input:** Plan from Meta-Layer
- **Responsibility:**
  - Load specified files and memory
  - Assemble context package
  - Route to correct execution path
- **Output:** Full context package for LLM
- **Key property:** Deterministic, reproducible, fast

### Layer 4: Execution Layer (LLM)
- **Type:** LLM call
- **Input:** Full context package + action plan
- **Responsibility:**
  - Understand (prediction over prepared context)
  - Generate response / decision
- **Output:** Final response or action
- **Key property:** Receives complete context — no impulsive tool calls

---

## Full Architecture Flow

```
User Input
    ↓
┌───────────────────────────────┐
│  Layer 1: Meta-Layer (Thinking)   │
│  Separate LLM call — planning,   │
│  reflection, scenarios, ethics    │
└───────────────────────────────┘
    ↓ plan
┌───────────────────────────────┐
│  Layer 2: Feeling Layer (D8_V7+)  │
│  Persistent emotional state —     │
│  weights plan, adds moral urgency  │
│  modulates tone, trust, ethics    │
└───────────────────────────────┘
    ↓ weighted plan + emotion params
┌───────────────────────────────┐
│  Layer 3: Deterministic Layer     │
│  Load context, files, memory      │
│  Assemble full context package    │
└───────────────────────────────┘
    ↓ full context package
┌───────────────────────────────┐
│  Layer 4: Execution (LLM)         │
│  Receives plan + emotion params   │
│  + full context. Only generates.  │
└───────────────────────────────┘
    ↓ response
Post-Processing: update D8 state, write memory
```

---

## Contrast with Current Architecture

| | Current (Impulsive) | BIAI |
|---|---|---|
| Planning | Implicit, within generation | Explicit, separate call (L1) |
| Feeling/Ethics | None | Persistent D8_V7+ state (L2) |
| Memory | LLM decides impulsively | Deterministic Layer loads (L3) |
| Tool calls | Reactive, mid-generation | Pre-planned in Meta-Layer |
| Context | Whatever fits in window | Curated, structured package |
| Reproducibility | Low | High |

---

## Integration with CideOWorld v5

- Meta-Layer: pre-call before `/decide` — dedicated planning prompt
- Deterministic Layer: context builder (state, trends, critical params)
- Execution Layer: existing Bridge call with enriched payload

Experiment: compare WOAI scores with/without Meta-Layer active.
