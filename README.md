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

## Experiment 03 — Multi-Agent & Runtime Governance Stress Testing

A multi-agent and extended workflow experiment evaluating how agents behave under **increased coordination complexity, longer execution chains, and real-world-style runtime conditions**.

This experiment expands beyond single-agent execution to test:

- multi-step and multi-agent workflows  
- dependency handling and intermediate state transitions  
- persistence of behavioral failure patterns across extended execution  
- robustness of governance signals under higher system complexity  

This phase shows:

> As execution complexity increases, outcome correctness becomes an increasingly unreliable proxy for behavioral validity.

Key observations:

- Failure modes compound across steps and agents, often without affecting final outcomes  
- Agents exhibit **consistent behavioral signatures** even in extended workflows  
- Governance-relevant signals (e.g., constraint violations, invalid transitions) scale with complexity  
- Multi-agent coordination introduces new classes of **latent execution failures** not captured by outcome-based metrics  

Dataset:

```
Experiment_03/run_review.csv
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

- *Runtime Verification for Autonomous AI Agents*  
- *Runtime Governance for Workflow-Realistic AI Agents*  
- *Multi-Agent Runtime Behavior & Governance (P-03)*  

---

# Notes

This repository contains only the data required to interpret experimental results at the level presented in the associated research.

Additional experiments and datasets will be added as the research program expands, including further work on **multi-agent systems, real-world execution environments, and governance infrastructure design**.
