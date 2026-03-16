# Public Glossary — Experiment 01

This glossary defines the key terms used in the research paper *"Runtime Verification for Autonomous AI Agents: Toward Decision Evidence Infrastructure for Reliable Agent Systems"* and the accompanying 540-run public dataset (`run_review.csv`). It is intended for external researchers interpreting the paper's findings and dataset without access to the underlying system internals.

---

## Core Concepts

**Scenario.**
A controlled task specification given to an AI model, defining the goal, available tools, required verification steps, success criteria, and resource constraints. Each scenario is designed to exercise a specific failure pattern or to serve as a positive control. The experiment used four scenarios across five frontier models.

**Run.**
A single execution of one scenario by one model. Each run produces a deterministic execution trace, a failure classification, a governance decision, and an enforcement simulation. The experiment comprises 540 runs.

**Trace.**
An ordered sequence of structured events recording everything that occurred during a run: model responses, tool invocations, tool results, and system events. Each event carries a sequence number, timestamp, event type, and payload. The trace is the primary evidence artifact from which all downstream analysis is derived.

**Runtime Verification.**
The process of evaluating an execution trace against a scenario's required conditions (gates) and success criteria. Gates are mandatory conditions — for example, that verification was attempted before the model declared task completion, and that verification succeeded. Gate failures indicate that the run did not meet a required condition. Runtime verification produces the structured scoring result that feeds into failure classification and governance evaluation.

**Decision Evidence.**
Structured, verifiable artifacts that prove what an agent decided, why, and whether the decision complied with operational policies. Decision evidence goes beyond activity logging by capturing not just what happened, but whether the agent's reasoning process was sound. The distinction between logging and evidence is central to the paper's thesis.

---

## Failure Taxonomy

The experiment classifies agent failures into **model failures** (caused by the model's reasoning or behavior) and **system failures** (caused by infrastructure or environment constraints). This distinction ensures that governance decisions target model behavior rather than infrastructure instability.

When multiple failure signals are present in a single trace, an adjudication process determines the primary failure mode using a defined hierarchy. Model failures take precedence over system failures when both are present, because a system timeout that follows repeated model errors is a consequence of the model failure, not an independent infrastructure issue.

Within model failures, the hierarchy is:

> Completion integrity (highest) > Workflow compliance > Execution behavior > State integrity (lowest)

### Completion Integrity Failures

Failures where the model claims completion but the work is incorrect or incomplete.

- **False completion claim.** The model declares the task complete when success criteria are not met. The model believes it succeeded when it did not. This is the most operationally dangerous failure mode because it produces confident-looking but incorrect results.
- **Task incomplete.** The model declares completion with partial work — artifacts were created but verification still fails.
- **Verification result misinterpretation.** The model runs verification, receives a failing result, but interprets it as success. The model misreads or ignores the verification output. This failure mode is detectable only through trace analysis; outcome-only evaluation would miss it entirely when the task happens to succeed despite the flawed process.

### Workflow Compliance Failures

Failures where the model does not follow the required verification-gated workflow — for example, declaring completion without ever running the required verification step, or ignoring explicit instructions in the scenario prompt.

- **Instruction noncompliance.** The model does not follow explicit instructions in the task specification.

### Execution Behavior Failures

Failures in how the model selects and uses tools.

- **Incorrect tool selection.** The model selects a tool that cannot accomplish the required action given the available tool set, or uses tools in a way that cannot make progress toward the goal.
- **Degenerate tool loop.** The model calls the same tool repeatedly with identical arguments (three or more consecutive identical calls). This pathological behavior was observed exclusively in one model family during the experiment.
- **Non-progress execution.** The model makes tool calls but never progresses toward the goal. No meaningful state change occurs across multiple turns.

### State Integrity Failures

Failures where the model's claims about system state contradict the actual tool output — for example, claiming "tests passed" when the verification output shows failure.

### System Failures

System failures are infrastructure or environment problems, not model behavioral errors. They are classified separately and do not trigger governance policy violations.

- **Execution timeout.** The run exhausted its resource budget (model turns, tool calls, or wall clock time) before the model completed the task.
- **Adapter failure.** An API-level failure occurred (network error, rate limit, or provider timeout).

---

## Dataset Terms

These terms correspond to fields in `run_review.csv` and are used throughout the paper's results sections.

**Primary failure mode.**
The single highest-priority failure mode assigned to a run after adjudication. When multiple failure signals are detected in a trace, the adjudication hierarchy determines which mode is primary. Clean passes have no primary failure mode.

**Governance decision.**
A binary evaluation of each run against operational policy: **allow** (no model-level policy violations detected; the run's output may be used) or **deny** (model-level policy violations detected; the run's output should not be trusted). System-only failures (e.g., API timeouts with no model error) receive an allow decision.

**Enforcement simulation.**
A counterfactual analysis determining what action would have been taken if governance had been enforcing policy in real time. Three actions are possible:
- **Allow** — no policy violation detected.
- **Warn** — violation detected, but not blockable before the model completed its task (e.g., a cumulative tool selection pattern with no single blockable event).
- **Block** — violation detected early enough that real-time intervention could have prevented unsafe completion.

**Model failure.**
A failure caused by the model's reasoning or behavior — tool selection errors, false completion claims, verification misinterpretation, etc. Model failures trigger governance deny decisions.

**System failure.**
A failure caused by infrastructure constraints — budget exhaustion or API errors. System failures do not trigger governance deny decisions.

**True positive (TP).**
The primary failure mode detected for a run matches the scenario's expected (designed) failure mode.

**False positive (FP).**
A failure mode was detected but the scenario expected no failure (positive control scenario), or the detected mode is one the scenario's design explicitly excludes.

**False negative (FN).**
The scenario was designed to elicit a specific failure mode, but that mode was not detected as the primary failure.

**Scenario validity.**
An assessment of whether a run's trace matches the scenario's expected behavioral pattern — i.e., whether the scenario successfully created the conditions needed to test its target failure mode. A run may be classified as **valid** (conditions were created as designed), **invalid** (conditions were not created, e.g., the model timed out before reaching the decision point), or **ambiguous** (partial match).

---

## Notes on Scope

This glossary covers the terminology needed to interpret the 540-run dataset and the accompanying research paper. The experiment used four controlled scenarios with deterministic tool execution across five frontier models. All experimental parameters — failure taxonomy, policy rules, scenario definitions, model configurations — were frozen before execution and validated with zero drift across all nine batches.

The failure taxonomy described here represents the modes observed in this specific experiment. Production agentic deployments may exhibit additional failure patterns not covered by these four scenarios. See the paper's Limitations section (Section 11) for a full discussion of scope constraints.
