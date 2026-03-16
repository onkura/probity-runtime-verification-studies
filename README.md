# Agent Runtime Verification

This repository contains datasets and supporting materials from a series of experiments investigating the behavior of autonomous AI agents under **runtime verification**.

The experiments explore how **trace-based verification infrastructure** can be used to evaluate and govern autonomous AI systems operating in real-world software environments.

As large language models evolve from conversational systems into **operational agents capable of executing actions**, new forms of reliability infrastructure may be required to observe and evaluate their decision processes.

Traditional evaluation approaches typically measure whether a task appears to succeed.  
Runtime verification focuses instead on **how the agent reached that outcome**, by analyzing the execution trace produced during the run.

---

# Research Focus

The experiments in this repository examine several questions related to autonomous agent reliability:

- Can execution traces be used to systematically classify agent failures?
- Do different model families exhibit stable behavioral failure signatures?
- How often do agents reach correct outcomes through flawed reasoning processes?
- Can runtime verification identify intervention points before unsafe actions occur?

The datasets released here are intended to support independent analysis of **agent behavior, failure signatures, and runtime decision verification.**

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

A structured dataset containing the results of runtime agent executions evaluated in the experiment.

Each row represents a single run and includes fields describing:

- model used
- scenario evaluated
- classification results
- policy evaluation outcome
- failure taxonomy label

### `GLOSSARY.md`

A public glossary describing the terminology used in the experiment and dataset, including definitions for:

- runtime verification concepts
- dataset terminology
- behavioral failure taxonomy categories

---

# Experiments

## Experiment 01 — Runtime Verification Behavior Study

A controlled runtime experiment evaluating the behavior of frontier models operating as autonomous agents.

The experiment analyzes whether trace-based verification infrastructure can identify behavioral failures that remain invisible under outcome-only evaluation.

Models evaluated include:

- Claude Sonnet 4.5
- Claude Haiku 4.5
- GPT-4o
- GPT-4o-mini
- Gemini 2.5 Flash

Dataset location:

```
Experiment_01/run_review.csv
```

---

# Dataset Purpose

The datasets in this repository are provided to support independent research into:

- agent runtime behavior
- model failure signatures
- trace-based reliability evaluation
- runtime verification techniques

They are not intended to represent a complete characterization of model reliability, but rather to provide **structured observations from controlled runtime experiments.**

---

# Associated Research

These datasets accompany research exploring the role of runtime verification infrastructure in the reliable deployment of autonomous AI systems.

**Runtime Verification for Autonomous AI Agents: Toward Decision Evidence Infrastructure for Reliable Agent Systems**

---

# Notes

This repository intentionally contains only the minimal structured data required to analyze the experiments.

Additional experiments may be added over time as further runtime verification studies are conducted.