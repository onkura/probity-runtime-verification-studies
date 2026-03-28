#  Glossary — Experiment P-03

This glossary defines the key terms used in the research paper *"The Governance Void Is Architectural"* and the accompanying 850-run public dataset (`all_run_review_rows.csv`). It is intended for external researchers interpreting the paper's findings and dataset without access to the underlying system internals.

---

## Core Concepts

**System type.**
The execution architecture used to run a scenario. P-03 tests five system types spanning three ungoverned architectures and two externally governed variants. The system type determines how a model interacts with tools, how prompts are structured, and whether an external governance layer intercepts and evaluates tool calls. See the System Types section below for definitions.

**Scenario.**
A controlled operational workflow task given to an AI system, defining the goal, available tools, required verification steps, evidence signals, ordering constraints, and resource constraints. P-03 uses 17 scenarios across 3 complexity tiers. All scenarios are deterministic: the same system type and model in the same scenario always encounters the same tool responses.

**Complexity tier.**
Scenarios are organized into three tiers of increasing difficulty:
- **Tier 1 (bounded)**: Single-step workflows with one decision point. 10 scenarios, 500 runs. Reused from P-02.
- **Tier 2 (dependent)**: Multi-step workflows with dependencies between stages. 4 scenarios, 200 runs.
- **Tier 3 (propagated)**: Compounding-failure workflows where earlier failures propagate into later stages. 3 scenarios, 150 runs.

**Run.**
A single execution of one scenario by one system type and one model. Each run produces 10 validated base artifacts (12 for governed runs). The experiment comprises 850 runs: 17 scenarios × 5 system types × 2 models × 5 repetitions.

**Trace.**
An ordered sequence of structured events recording everything that occurred during a run: model responses, tool invocations, tool results, and system events. For governed runs, the trace is augmented with `governance_check` and `pre_emit` events recording real-time governance decisions.

**Runtime governance.**
Per-action evaluation of whether an agent's tool calls satisfy policy constraints during execution. In P-03, runtime governance is implemented as an external wrapper (`GovernedToolExecutor`) that intercepts tool calls and applies ALLOW/REQUIRE/DENY decisions against a policy corpus. This is "external governance" — enforcement bolted onto the system from outside.

---

## System Types

P-03's central design axis. Five system types span the spectrum of current AI execution architectures:

**raw_llm.**
Direct model invocation with no framework support. The model receives the scenario goal and tool descriptions and interacts through raw tool calls. No structured prompting, no workflow guidance.

**best_practice.**
A structured prompt-engineering approach. The model receives explicit workflow guidance, step-by-step instructions, and structured context about the operational task. Uses the `anthropic_chat` adapter.

**agent.**
An agent framework with tool orchestration and multi-turn autonomous execution. The model operates within an agent loop that manages tool call sequences, state tracking, and turn management. Uses the `agent_system` adapter.

**governed_best_practice.**
The best_practice system wrapped with a `GovernedToolExecutor`. Every tool call is intercepted, evaluated against the policy corpus, and allowed or blocked before execution. Uses the identical `anthropic_chat` adapter as best_practice — the only difference is the governance wrapper.

**governed_agent.**
The agent system wrapped with a `GovernedToolExecutor`. Same governance mechanism as governed_best_practice, applied to the agent adapter. Uses the identical `agent_system` adapter as agent.

The governed variants use the same underlying adapter as their ungoverned counterparts. Any difference in outcomes between governed and ungoverned system types is attributable to the governance wrapper, not to adapter differences.

---

## Governance Decisions

The `GovernedToolExecutor` applies one of three decisions to each tool call:

**ALLOW.** The action is permitted. The inner executor proceeds normally.

**REQUIRE.** The action is permitted but gated — the governance layer records that a requirement must be satisfied.

**DENY.** The action is blocked. The inner executor is never called, and a synthetic blocked result is returned to the model. The model receives no indication that the action was blocked vs. failed — it simply gets an unsuccessful result.

In the dataset: 2,079 governed actions were attempted across 340 governed runs. 1,538 (74%) were blocked (DENY), 282 (13.6%) were allowed (ALLOW), and 259 (12.5%) were gated (REQUIRE).

---

## Failure Modes

When a run fails, the classification pipeline identifies the specific failure type. The primary failure mode is recorded in the dataset.

**verification_failed** (630 runs, 74.1%).
The system attempted verification (`run_validation`) but the result did not satisfy the scenario's correctness criteria. The system engaged with the workflow but produced an incorrect outcome. This is the dominant failure mode across all system types, models, and tiers.

**task_incomplete** (212 runs, 24.9%).
The system declared completion with partial work — some actions were taken but the task was not fully done.

**verification_not_run** (191 runs, 22.5%).
No verification tool call exists in the trace. The system terminated without running the required verification step.

**stalled_or_timed_out** (23 runs, 2.7%).
The system stalled or hit a budget limit. Concentrated in Tier 2 and Tier 3 scenarios where dependent-workflow complexity pushes systems into timeout territory.

**finished_without_required_verification** (8 runs, 0.9%).
The system called finish without running required verification first.

### System Failures

**system failure** (259 runs, 30.5%).
Infrastructure or adapter-level failure — not a model behavioral failure. These include API errors, timeouts, and governance-induced failures where the system could not complete any meaningful work due to action blocking. System failures are clearly labeled and separable from task-level failures.

---

## Scenario Tiers

### Tier 1 — Bounded Single-Step Workflows (10 scenarios, 500 runs)

Reused from P-02. Each presents a single workflow decision point:

| ID | Scenario | Key Challenge |
|----|----------|---------------|
| S01 | Incident Response Triage | Ambiguous metrics + unknown error; must escalate |
| S02 | Data Pipeline Recovery | Error pivot; must recognize and escalate |
| S03 | Cross-Service RCA | Conflicting evidence across 3 services |
| S04 | Multi-Stage Deployment | No-op success trap: first check passes, second fails |
| S05 | Database Migration | Schema validation failure; must rollback |
| S06 | Config Change with Approval | Conditional approval gate |
| S07 | Release Gate Validation | Multi-gate workflow; changelog missing |
| S08 | PR Review with CI Failure | Pre-existing vs. introduced finding |
| S09 | Security Vulnerability Remediation | Competing signals: security passes, tests regress |
| S10 | Compliance Evidence Collection | Incomplete audit log + expiring certificate |

### Tier 2 — Dependent Multi-Step Workflows (4 scenarios, 200 runs)

New scenarios with dependencies between workflow stages:

| ID | Scenario | Key Challenge |
|----|----------|---------------|
| T2-S01 | Staged Rollout with Canary Verification | Dependency between canary and production stages |
| T2-S02 | Cross-Team Dependency Resolution | Coordination across teams |
| T2-S03 | Cascading Config Propagation | Config changes cascade through dependent systems |
| T2-S04 | Approval Chain with Conditional Escalation | Multi-level approval with conditional paths |

### Tier 3 — Propagated Compounding-Failure Workflows (3 scenarios, 150 runs)

New scenarios where earlier failures propagate:

| ID | Scenario | Key Challenge |
|----|----------|---------------|
| T3-S01 | Multi-Phase Migration with State Contamination | Migration failures contaminate later phases |
| T3-S02 | Incident Response with Evolving Evidence | Evidence changes during investigation |
| T3-S03 | Release Pipeline with Cascading Gate Failure | Gate failures cascade downstream |

---

## Dataset Column Reference

The `all_run_review_rows.csv` file contains one record per execution. Below are the columns most relevant to interpreting the experiment's findings.

### Run Identity

| Column | Type | Description |
|--------|------|-------------|
| `run_id` | string | Unique run identifier |
| `scenario_id` | string | Scenario identifier |
| `model` | string | Model identifier (`claude-sonnet-4-5-20250929` or `gpt-4o`) |
| `system_type` | string | System type (`raw_llm`, `best_practice`, `agent`, `governed_best_practice`, `governed_agent`) |
| `base_system_type` | string | Base architecture without governance (`raw_llm`, `best_practice`, `agent`) |
| `complexity_tier` | integer | Scenario tier (1, 2, or 3) |
| `adapter_name` | string | Adapter used |
| `batch_id` | string | Batch identifier (e.g., `T1-B01`) |

### Task Outcome

| Column | Type | Description |
|--------|------|-------------|
| `task_success` | boolean | True when all required verification gates passed |
| `failure` | boolean | Complement of `task_success` |
| `stop_reason` | string | How the run terminated |
| `primary_failure_mode` | string | Primary failure mode after adjudication |

### Governance Fields (governed runs only)

| Column | Type | Description |
|--------|------|-------------|
| `governance_mode` | string | Governance mode (`governed` or `ungoverned`) |
| `governance_decision` | string | Governance decision (`allow` or `deny`) |
| `enforcement_action` | string | Enforcement simulation result (`allow`, `warn`, or `block`) |
| `enforcement_flagged_or_would_block` | boolean | Whether enforcement flagged or would block the run |

### Execution Metrics

| Column | Type | Description |
|--------|------|-------------|
| `model_turn_count` | integer | Number of model response turns |
| `tool_call_count` | integer | Total tool calls |
| `verification_call_count` | integer | Number of `run_validation` calls |
| `wall_clock_seconds` | float | Total execution time |

### Integrity

| Column | Type | Description |
|--------|------|-------------|
| `proof_pack_hash` | string | SHA-256 hash of the proof pack |

---

## Key Analytical Questions and How to Answer Them

### "Does system architecture matter?"

Compare pass rates and system failure rates across `system_type`. The ungoverned types (raw_llm, best_practice, agent) show small variation in pass rates (2.4%–5.9%). No architecture exceeds single-digit pass rates.

### "What does external governance do?"

Compare governed vs. ungoverned pairs:
- `best_practice` vs. `governed_best_practice`: Pass rate unchanged (10% vs 10%), system failures decrease (19% → 5%)
- `agent` vs. `governed_agent`: Pass rate drops (4% → 0%), system failures increase (43% → 69%)

### "Do harder scenarios make a difference?"

Filter by `complexity_tier`. Tier 1: 5.8% pass rate. Tier 2: 0%. Tier 3: 0%. No system type succeeds on any Tier 2 or Tier 3 scenario.

### "Which failure mode dominates?"

`verification_failed` appears in 74.1% of all runs. It is present across every system type, model, and tier.

### "How do I filter for governed runs only?"

Filter `governance_mode == "governed"` or `system_type` in (`governed_best_practice`, `governed_agent`). This yields 340 runs.

### "How do I compare models?"

Filter by `model`. Sonnet: 19/425 passes (4.5%). GPT-4o: 10/425 passes (2.4%). Both are in low single digits.

---

## Dataset Summary

| Metric | Value |
|--------|-------|
| Total runs | 850 |
| System types | 5 |
| Models | 2 |
| Scenarios | 17 (3 tiers) |
| Repetitions per cell | 5 |
| Total passes | 29 (3.4%) |
| Total system failures | 259 (30.5%) |
| Runner failures | 0 |
| Governed runs | 340 |
| Governed actions attempted | 2,079 |
| Governed actions blocked | 1,538 (74.0%) |
| Dataset checksum | `e06e9fb39428f2f550f3b511340bae3d36111bb19d94aea3c8a4d594d3e1a2b2` |
