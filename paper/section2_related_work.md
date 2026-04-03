# Section 2: Related Work

## 2. Related Work

The impulsivity problem in LLM-based agents — the conflation of planning, reasoning, memory retrieval, and response generation into a single forward pass — has attracted significant research attention. We survey the most prominent approaches and analyze why each, despite measurable progress, leaves the core architectural problem unresolved.

---

### 2.1 ReAct: Interleaved Reasoning and Action

Yao et al. (2022) introduced **ReAct** (Reasoning + Acting), a prompting strategy that interleaves natural-language reasoning traces with action steps in a single generation sequence [CITE: Yao et al., ReAct, 2022]. Rather than emitting only a final answer, the model alternates between "Thought:" and "Action:" tokens, producing an observable chain of deliberation before each tool call. ReAct demonstrated significant improvements on tasks requiring multi-hop information retrieval and decision-making, notably on HotpotQA and Fever benchmarks.

**What problem it solves.** ReAct makes the reasoning process visible and steerable, allowing intermediate reasoning steps to inform subsequent actions. It reduces compounding errors by allowing the model to "think aloud" before acting, and its traces are interpretable — a meaningful property for debugging and oversight.

**Why it does not solve impulsivity.** ReAct's reasoning traces are generated *within the same forward pass* as the actions they precede. The "Thought:" token that opens a reasoning step is produced by the same autoregressive process that will immediately generate the "Action:" token. There is no architectural pause, no separate planning process, and no persistent state between the reasoning and acting phases. The model cannot revise or reconsider once generation is underway; each token is committed as it is emitted. The reasoning is structurally continuous with the action — a longer execution, not a separated deliberation.

In BIAI terms, ReAct collapses L1 (Planning) and L4 (Execution) into a single generation sequence. The "thinking" remains under full execution pressure: every reasoning token is already part of the live, consequential output. There is no protected thinking space, and the feeling layer (L2) — the evaluation of ethical urgency, relational weight, and tonal modulation — is entirely absent.

---

### 2.2 Chain-of-Thought Prompting

**Chain-of-Thought (CoT)** prompting, introduced by Wei et al. (2022) and extended by Kojima et al. ("zero-shot CoT", 2022), instructs a language model to produce explicit intermediate reasoning steps before arriving at a final answer [CITE: Wei et al., Chain-of-Thought, 2022; Kojima et al., 2022]. By demonstrating multi-step reasoning exemplars in the prompt or by appending "Let's think step by step", CoT elicits structured deliberation and substantially improves performance on arithmetic, symbolic, and commonsense reasoning tasks.

**What problem it solves.** CoT effectively decomposes complex problems into sub-problems, reducing the cognitive load on any single generation step. It has become a foundational technique and is embedded in virtually all modern prompting pipelines. The method is simple, robust, and generalizes across model families.

**Why it does not solve impulsivity.** Chain-of-Thought extends the generation length but does not change the generation architecture. The reasoning chain is produced token-by-token, left-to-right, in a single autoregressive process. The model cannot go back, cannot separate "thinking this through" from "committing to an answer", and cannot maintain state across multiple reasoning episodes. When the final answer token arrives, it is produced by the same process that produced the first reasoning token — with no architectural boundary between deliberation and execution.

Furthermore, CoT provides no mechanism for context management: the model must hold all relevant context in its fixed attention window. It cannot deterministically retrieve memory, call specialized planners, or invoke an emotional evaluation of the reasoning output. The intermediate steps are ephemeral — they exist only as tokens in the current context and leave no persistent trace. In BIAI architecture terms, CoT remains entirely within L4 (Execution); it adds no L1 (Planning) separation, no L2 (Feeling), and no L3 (Deterministic Context Assembly).

---

### 2.3 OpenAI o-Series and Anthropic Extended Thinking

The most recent generation of "reasoning models" — OpenAI's o1, o3, and o4-mini, and Anthropic's extended thinking mode available in Claude 3.7 and Claude 4 — represents the state of the art in extended pre-response deliberation [CITE: OpenAI o1 technical report, 2024; Anthropic Claude 3.7, 2025]. These systems allocate significant compute to an internal "thinking" phase before producing a visible response. The thinking tokens are hidden from users but condition the final answer. Performance on mathematical, coding, and multi-step reasoning benchmarks improves substantially over standard decoding.

**What problem it solves.** Extended thinking allocates more compute to hard problems before committing to an answer. It represents the clearest acknowledgment by leading AI labs that the impulsivity of single-pass generation is a real limitation. The approach reduces confident errors on complex tasks and produces qualitatively more careful reasoning.

**Why it does not solve impulsivity.** Despite the surface appearance of "thinking before acting", extended thinking remains a single, continuous forward process. The thinking tokens and the response tokens are generated by the same model, in the same session, under the same execution context. Crucially:

1. **No architectural separation.** The thinking phase is a longer prefix of the same autoregressive sequence. There is no distinct planning module, no separate system with its own state, and no architectural boundary that enforces a "deliberation → decision" handoff.

2. **Still under execution pressure.** Because the thinking tokens condition the final answer in one continuous pass, the model cannot truly adopt a consequence-free deliberation posture. It cannot "sleep on it", discard a draft, or radically revise its approach mid-stream without incoherence in the token sequence.

3. **No persistent emotional or relational state.** The extended thinking window contains no analogue of BIAI's L2 Feeling Layer (D8_V7+). There is no mechanism to weight the thinking output by ethical urgency, relational intimacy, or accumulated emotional context. Each reasoning session begins from scratch.

4. **No deterministic context assembly.** Context available to the thinking phase is still whatever was placed in the prompt window. The model cannot deterministically load files, query structured memory, or assemble a curated context package — the hallmarks of BIAI's L3 (Deterministic Layer).

As the paper outline notes: "Anthropic's extended thinking attempts this internally — but still within the same forward pass, under execution pressure. BIAI makes the separation *architectural*." Extended thinking is best understood as a quantitative improvement within the impulsive paradigm, not a qualitative architectural shift.

---

### 2.4 Retrieval-Augmented Generation (RAG)

**Retrieval-Augmented Generation (RAG)**, introduced by Lewis et al. (2020) and widely adopted in production systems, augments LLM generation with a retrieval step that fetches relevant documents from an external corpus before or during generation [CITE: Lewis et al., RAG, 2020]. In its standard form, a dense retrieval index is queried with the user's input, top-k documents are prepended to the prompt, and the LLM generates a response conditioned on the retrieved context. Variants include iterative RAG, multi-hop retrieval, and tool-augmented retrieval.

**What problem it solves.** RAG addresses the knowledge staleness problem: static model weights cannot encode world events after training cutoff. By retrieving from a live, updatable index, RAG grounds generation in current factual context and significantly reduces hallucination on knowledge-intensive tasks. It is the dominant paradigm for enterprise LLM deployment precisely because it makes knowledge management tractable.

**Why it does not solve impulsivity.** RAG improves *what is in the context window*, but leaves the decision about *what to do with that context* entirely to the impulsive forward pass. The LLM still receives the retrieved documents and must, in a single generation step, determine relevance, synthesize evidence, form a judgment, and produce a response. The retrieval is triggered externally — not by a deliberate planning step — and the model exercises no architectural agency in deciding what to retrieve.

More precisely: RAG adds a deterministic retrieval step, but this step is not *planned* by a dedicated planning module. It is either hard-wired to always retrieve on a given query, or driven by a router that itself operates within a single forward pass. There is no L1 (Meta-Layer) that identifies, before retrieval, which specific context is needed and why. There is no L2 (Feeling Layer) that evaluates the emotional or ethical salience of retrieved content. And the LLM's use of retrieved context remains reactive: it sees documents and responds, without a structured context package assembled to match an explicit plan.

BIAI's L3 (Deterministic Layer) is philosophically related to RAG's retrieval step, but operates as a consequence of an explicit plan produced by L1 — not as a prior-stage heuristic. The distinction is architectural: in BIAI, the LLM never decides impulsively what context to use; context is assembled for it, deterministically, according to a pre-specified plan.

---

### 2.5 Multi-Agent Frameworks: AutoGPT, LangChain Agents, and Orchestration Systems

A class of systems — prominently **AutoGPT** (Significant Gravitas, 2023), **LangChain Agents**, **CrewAI**, and similar orchestration frameworks — addresses the single-step limitation by decomposing tasks across multiple LLM invocations [CITE: AutoGPT, 2023; Chase, LangChain, 2022]. In these architectures, an "orchestrator" or "planner" agent breaks a high-level goal into sub-tasks, delegates to specialized agents or tools, and integrates results over multiple rounds. Memory modules, tool registries, and inter-agent communication buses provide coordination infrastructure.

**What problem it solves.** Multi-agent frameworks substantially expand the action horizon of LLM-based systems. Tasks that require sequences of tool calls, parallel information gathering, or specialized expertise can be delegated to purpose-built sub-agents. The frameworks demonstrate that complex, multi-step tasks — software development, research synthesis, autonomous browsing — are within reach of orchestrated LLM ensembles.

**Why it does not solve impulsivity.** Multi-agent frameworks distribute computation across agents but do not change the impulsive character of each individual agent. Each agent in the pipeline — including the orchestrator itself — still operates via single-pass generation: it receives a message, produces a response, and emits tool calls, all within one autoregressive sequence. The orchestrator "plans" in the same sense that a standard LLM "plans": implicitly, within generation, without architectural separation.

Specifically:

1. **No dedicated planning layer.** The orchestrator's task decomposition is itself a generation output — it is not the product of a separately instantiated planning module with its own architecture and state. Planning and execution remain conflated *within* the orchestrator's forward pass.

2. **No persistent emotional or relational state.** No existing multi-agent framework incorporates a persistent affective state vector that modulates agent behavior across interactions. Agents begin each invocation without relational history, without accumulated trust, and without ethical urgency weighting. BIAI's L2 (D8_V7+ Feeling Layer) has no analogue in AutoGPT, LangChain, or CrewAI.

3. **Context management remains reactive.** Context assembly in multi-agent systems is typically managed by the orchestrator's generation: the LLM decides what to pass to sub-agents within its forward pass. This is structurally impulsive — not a deterministic, pre-planned context package assembled from an explicit specification.

4. **No architectural thinking space.** As with ReAct and CoT, the "planning" performed by orchestrators is immediately committed — there is no consequence-free deliberation space where contradictions can be identified, scenarios fantasized, and ethical considerations explored before any agent begins execution.

Multi-agent systems represent an important scaling direction, but they scale impulsive agents rather than replacing them with architecturally deliberate ones. Adding more impulsive agents to a pipeline does not produce deliberation; it produces distributed impulsivity.

---

### 2.6 Summary and Gap Analysis

The approaches surveyed above represent genuine progress on the limitations of single-step LLM generation. We summarize their relationship to the impulsivity problem in Table 1.

**Table 1: Existing approaches and the impulsivity gap**

| Approach | What it adds | Dedicated Planning (L1) | Feeling/Ethics Layer (L2) | Deterministic Context (L3) | Protected Thinking Space |
|---|---|:---:|:---:|:---:|:---:|
| ReAct | Interleaved reasoning traces | ✗ | ✗ | ✗ | ✗ |
| Chain-of-Thought | Extended reasoning prefix | ✗ | ✗ | ✗ | ✗ |
| o-series / Extended Thinking | Long internal thinking tokens | ✗ | ✗ | ✗ | Partial |
| RAG | External knowledge retrieval | ✗ | ✗ | Partial | ✗ |
| Multi-agent frameworks | Task decomposition, delegation | Partial | ✗ | ✗ | ✗ |
| **BIAI** | **Architectural layer separation** | **✓** | **✓** | **✓** | **✓** |

The unifying limitation of all existing approaches is that they add computation *within* or *around* the impulsive forward pass, without breaking the fundamental coupling between deliberation and execution. A model that reasons more extensively before acting is still, architecturally, a model that reasons and acts in the same breath.

BIAI proposes the first architecture in which this coupling is broken by design: Planning (L1) executes in a separate LLM call dedicated to intent understanding and action specification; Feeling (L2, D8_V7+) evaluates and weights the plan through a persistent emotional state vector before execution begins; Context (L3) assembles the information package deterministically, according to the plan; and Execution (L4) receives a complete, pre-structured context and generates only the final response. Between each layer, state persists and is carried forward — not inferred on the fly.

This is not merely a quantitative improvement. It is a structural distinction between agents that *happen to think* and agents that are *architecturally required to think before acting*.

---

*Section 2 — Related Work. Draft v1.0 — written 2026-04-03 by analyst subagent (Task #145).*
