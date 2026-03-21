# Agent Runtime Verification & Governance

This repository contains datasets and supporting materials from a series of experiments investigating the behavior of autonomous AI agents under **runtime verification and governance**.

As large language models evolve from conversational systems into **operational agents capable of executing actions**, new forms of infrastructure are required to evaluate not only whether tasks succeed, but whether agents behave correctly during execution.

Traditional evaluation approaches measure outcomes.  
These experiments focus instead on **runtime behavior** — how agents gather evidence, follow workflow constraints, make decisions, and handle failure conditions.

---

# Research Focus

The experiments in this repository examine several questions related to autonomous agent reliability:

- Can execution traces be used to systematically classify agent failures?
- Do different model families exhibit stable behavioral failure signatures?
- How often do agents reach correct outcomes through flawed decision processes?
- Can outcome-based evaluation misrepresent agent behavior in workflow settings?
- What additional infrastructure is required to evaluate and govern agent decisions at runtime?

The datasets released here are intended to support analysis of **agent behavior, failure signatures, and runtime decision evaluation** under controlled conditions.

---

# Repository Structure

Each experiment is contained within its own directory.

```
Experiment_XX/
│
├── run_review.csv
└── GLOSSARY.md
```

### `run_review.csv`

A structured dataset containing **run-level summaries** of agent executions.

Each row represents a single run and includes fields describing:

- model used
- scenario evaluated
- legacy outcome-based result
- structural validity classification
- governance-relevant outcome signals
- failure taxonomy labels

The dataset is designed to support reconstruction of **aggregate results reported in the associated research papers**, including model-level and scenario-level distributions.

### `GLOSSARY.md`

A public glossary describing the terminology used in the experiment and dataset, including:

- runtime verification and governance concepts
- dataset terminology
- evaluation layers and outcome categories
- behavioral failure taxonomy

---

# Experiments

## Experiment 01 — Runtime Verification Behavior Study

A controlled runtime experiment evaluating whether trace-based verification can identify behavioral failures that are not visible under outcome-only evaluation.

This experiment establishes that:

> Agents can reach correct outcomes through flawed internal processes.

Models evaluated include:

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

This experiment introduces a governance-aware evaluation lens and shows that:

> Outcome-based scoring systematically misclassifies agent behavior by failing to distinguish true failure, invalid execution, and correct remediation.

Key findings include:

- Outcome-based evaluation overstates failure by collapsing multiple behavior types into a single label
- A significant portion of runs represent **correct remediation behavior**, not failure
- Structural workflow validity and decision correctness are separable dimensions of agent performance

Dataset:

```
Experiment_02/run_review.csv
```

---

# Dataset Scope

The datasets in this repository provide **structured run-level summaries** of controlled experiments.

They are designed to:

- support independent analysis of reported results
- enable reconstruction of aggregate findings
- expose behavioral patterns across models and scenarios

They are **not intended to include full raw execution traces or internal system implementations**.

---

# Associated Research

These datasets accompany research on the role of runtime verification and governance infrastructure in the reliable deployment of autonomous AI systems.

- *Runtime Verification for Autonomous AI Agents*
- *Runtime Governance for Workflow-Realistic AI Agents*

---

# Notes

This repository intentionally contains only the data required to interpret experimental results at the level presented in the associated research.

Additional experiments and datasets may be added over time as the research program expands.
