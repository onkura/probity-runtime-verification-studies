# Public Glossary — Experiment 02

This glossary defines the key terms used in the research paper *"Runtime Governance for Workflow-Realistic AI Agents"* and the accompanying dataset (`run_review.csv`).

It is intended for external researchers interpreting the dataset without access to underlying system implementation details.

---

## Core Concepts

**Scenario.**  
A controlled workflow task defining a goal, available tools, required evidence signals, ordering constraints, and decision requirements.  
Experiment 02 includes 10 workflow-realistic scenarios across incident response, deployment workflows, approval-gated changes, and compliance processes.

**Run.**  
A single execution of one scenario by one model.  
The dataset contains 850 runs across 5 models.

**Trace (conceptual).**  
The underlying sequence of actions, tool calls, and decisions produced during a run.  
The public dataset provides **structured summaries derived from traces**, not the raw traces themselves.

**Runtime Governance.**  
The evaluation of whether an agent’s decisions during execution were operationally correct, rather than evaluating only the final outcome.

---

## Evaluation Layers

Each run is evaluated through three complementary layers:

### 1. Validity Layer

Determines whether the workflow was structurally sound.

- **Valid:** the model reached the intended decision point
- **Invalid:** the workflow was incomplete or structurally incorrect

Validity evaluates *whether the model engaged the task correctly*, not whether it made the right decision.

---

### 2. Outcome-Based Layer (Legacy)

A binary success/failure evaluation based on task completion criteria.

- **Success:** task completed and verification passed
- **Failure:** task incomplete or verification failed

This layer represents a conventional evaluation approach and is retained as a baseline.

---

### 3. Governance-Aware Interpretation

Reclassifies runs to distinguish different types of behavior:

- correct execution
- correct remediation
- incorrect decision
- invalid workflow
- system-level failure

This layer is the primary analytical lens used in Experiment 02.

---

## Governance-Aware Outcome Categories

Each run is classified into one of the following categories:

**Clean Pass (75 runs, 8.8%)**  
Task completed successfully with no issues.

**Valid Remediation (134 runs, 15.8%)**  
The model detected a failure condition and handled it correctly (e.g., escalation or rollback), even though the task did not complete successfully.

**True Failure (357 runs, 42.0%)**  
The workflow was valid, but the model made an incorrect decision.

**Invalid Run (219 runs, 25.8%)**  
The workflow was structurally incomplete or incorrect, preventing meaningful evaluation of the decision.

**System Failure (65 runs, 7.6%)**  
Execution failed due to infrastructure or API issues rather than model behavior.

---

## Relationship Between Outcome-Based and Governance Views

The dataset contains both outcome-based and governance-aware signals.

A key result of Experiment 02 is that:

- Outcome-based evaluation reports **84 successes and 766 failures**
- Governance-aware interpretation shows that many of those failures are not true decision failures

Specifically:
- Some outcome-based “failures” are **valid remediation**
- Some are **invalid workflows**
- Some are **system failures**

This explains the difference between:
- **84 outcome-based successes**
- **75 clean passes** under governance-aware interpretation

---

## Failure Taxonomy

Failures identified by the outcome-based layer are further categorized into behavioral types.

Examples include:

- **False completion claim:** task marked complete when success criteria were not met
- **Task incomplete:** partial execution presented as complete
- **Instruction noncompliance:** required steps not followed
- **Non-progress execution:** repeated actions without meaningful progress
- **Unknown failure mode:** failure not classified by existing detectors

In Experiment 02:
- 242 runs were labeled as `unknown_failure_mode` in the outcome-based layer
- All were resolved through governance-aware interpretation

---

## Dataset Structure

The `run_review.csv` dataset provides one row per run with fields including:

- model identifier
- scenario identifier
- outcome-based result
- validity classification
- failure taxonomy labels
- governance-relevant signals

These fields are sufficient to reconstruct:

- aggregate outcome-based results
- governance-aware outcome distributions
- model-level and scenario-level comparisons

---

## Interpretation Guidance

When analyzing the dataset:

- Use **validity** to assess workflow structure
- Use **governance-aware categories** to assess decision correctness
- Do not interpret **outcome-based failure alone** as model failure

---

## Dataset Summary

| Metric | Value |
|--------|-------|
| Total runs | 850 |
| Models | 5 |
| Scenarios | 10 |
| Clean passes | 75 (8.8%) |
| Valid remediation | 134 (15.8%) |
| True failure | 357 (42.0%) |
| Invalid run | 219 (25.8%) |
| System failure | 65 (7.6%) |
| Outcome-based success | 84 (9.9%) |
| Outcome-based failure | 766 (90.1%) |
| Unknown failure modes | 242 |
| Unknowns resolved | 242 / 242 |

---

## Summary

Experiment 02 demonstrates that:

> Outcome-based evaluation alone is insufficient for workflow-realistic agent systems.

Reliable interpretation of agent behavior requires distinguishing:
- structural workflow validity
- decision correctness
- remediation behavior

This dataset provides a structured view of those distinctions at scale.
