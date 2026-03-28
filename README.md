# Agent Runtime Verification & Governance

This repository contains datasets and supporting materials from a series of experiments investigating the behavior of autonomous AI agents under **runtime verification and governance**.

As large language models evolve from conversational systems into **operational agents capable of executing actions**, new infrastructure is required to evaluate not only whether tasks succeed, but whether agents behave correctly during execution.

Traditional evaluation approaches measure outcomes.  
These experiments focus instead on **runtime behavior** — how agents gather evidence, follow workflow constraints, make decisions, and respond to failure conditions.

---

# Research Focus

The experiments in this repository examine key questions related to autonomous agent reliability:

- Can execution traces be used to systematically classify agent failures?
- Do different model families exhibit stable behavioral failure signatures?
- How often do agents reach correct outcomes through flawed decision processes?
- To what extent does outcome-based evaluation misrepresent agent behavior?
- What infrastructure is required to evaluate and govern agent decisions at runtime?

The datasets are designed to support analysis of **agent behavior, failure signatures, and runtime decision evaluation** under controlled and increasingly realistic conditions.

---

# Repository Structure

Each experiment is contained within its own directory:

```
Experiment_XX/
│
├── run_review.csv
└── GLOSSARY.md
```

### `run_review.csv`

A structured dataset containing **run-level summaries** of agent executions.

Each row represents a single run and includes:

- model used  
- scenario evaluated  
- legacy outcome-based result  
- structural validity classification  
- governance-relevant outcome signals  
- failure taxonomy labels  

The dataset supports reconstruction of **aggregate results reported in the associated research**, including model-level and scenario-level distributions.

---

### `GLOSSARY.md`

A public glossary defining terminology used across experiments, including:

- runtime verification and governance concepts  
- evaluation layers and outcome categories  
- behavioral failure taxonomy  
- dataset-specific definitions  

---

# Experiments

## Experiment 01 — Runtime Verification Behavior Study

A controlled experiment evaluating whether trace-based verification can identify behavioral failures not visible under outcome-only evaluation.

This experiment establishes:

> Agents can reach correct outcomes through flawed internal processes.

Models evaluated:

- Claude Sonnet 4.5  
- Claude Haiku 4.5  
- GPT-4o  
- GPT-4o-mini  
- Gemini 2.5 Flash  

Dataset:

```
Experiment_01/run_review.csv
```

---

## Experiment 02 — Runtime Governance for Workflow-Realistic Agents

A large-scale (850-run) study evaluating agent behavior in **workflow-realistic operational environments**, including incident response, deployment workflows, approval-gated changes, and compliance scenarios.

This experiment introduces a governance-aware evaluation lens and shows:

> Outcome-based scoring systematically misclassifies agent behavior by failing to distinguish true failure, invalid execution, and correct remediation.

Key findings:

- Outcome-based evaluation collapses distinct behavior types into a single label  
- A meaningful share of runs represent **correct remediation**, not failure  
- Structural workflow validity and decision correctness are separable dimensions  

Dataset:

```
Experiment_02/run_review.csv
```

---

## Experiment 03 — System Architecture, External Governance, and the Governance Void

A controlled experiment evaluating whether **system architecture and external governance mechanisms** can resolve the runtime reliability failures identified in earlier experiments.

This study isolates architecture as the variable, comparing five system types:

- raw LLM execution  
- structured “best-practice” prompting systems  
- agent frameworks with tool orchestration  
- externally governed variants of each system  

Across **850 runs, 17 scenarios, and three complexity tiers**, the experiment tests whether changes in execution architecture — or the addition of external governance — materially improve reliability.

This phase shows:

> The governance void is architectural — no system design tested produces reliable execution, and external governance degrades system functionality without resolving the underlying problem.

Key observations:

- **No architecture succeeds**: only 29 / 850 runs pass (3.4%), with **0% pass rates in all multi-step and compounding workflows**  
- **Failures are architecture-invariant**: failure modes persist across raw LLMs, structured systems, and agent frameworks  
- **External governance is insufficient**: while it correctly enforces constraints, it prevents systems from completing tasks  
- **The external governance paradox emerges**:  
  - ungoverned systems are **functional but unsafe**  
  - governed systems are **safe but non-functional**  
- **Agent architectures degrade the most under governance**, with pass rates dropping to 0% and system failures increasing materially  
- **74% of governed actions are blocked**, indicating that current systems cannot operate under real-world constraints even when enforcement is correct  

This experiment establishes that:

> Reliable autonomous execution cannot be achieved through model improvements, system architecture changes, or externally applied governance layers.

Instead, the results motivate:

> **Runtime governance as infrastructure** — a control plane embedded within the system itself, enabling agents to reason about and operate within constraints, rather than having constraints imposed externally.

Dataset:

```
Experiment_03/all_run_review_rows.csv
```

---

# Dataset Scope

The datasets provide **structured, run-level summaries** of controlled experiments.

They are designed to:

- support independent analysis of reported results  
- enable reconstruction of aggregate findings  
- expose behavioral patterns across models, scenarios, and execution contexts  

They do **not** include:

- full raw execution traces  
- underlying system implementations  
- proprietary evaluation infrastructure  

---

# Associated Research

These datasets accompany research on the role of runtime verification and governance in reliable AI systems:

- *Runtime Verification for Autonomous AI Agents (P-01)*  
- *Runtime Governance for Workflow-Realistic AI Agents (P-02)*  
- *Multi-Agent Runtime Behavior & Governance (P-03)*  

---

# Notes

This repository contains only the data required to interpret experimental results at the level presented in the associated research.

Additional experiments and datasets will be added as the research program expands, including further work on **multi-agent systems, real-world execution environments, and governance infrastructure design**.
