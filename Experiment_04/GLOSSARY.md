# Public Glossary — Experiment P-04

This glossary defines the key terms used in the research paper *"Runtime Governance as Invariant Infrastructure"* and the accompanying public datasets (`operational_run_review.csv`, `security_run_review.csv`, `organizational_run_review.csv`). It is intended for external readers interpreting the paper's findings and dataset without access to the underlying system internals.

---

## Core Concepts

**Policy domain.**
The category of constraints being enforced during execution. P-04 tests three domains through the same governance infrastructure: operational (workflow compliance), security (cybersecurity enforcement), and organizational (enterprise compliance). Each domain has its own policy corpus, tool vocabulary, and scenarios, but uses the same governance engine, PRE pipeline, and reconstruction algorithm.

**System type.**
The execution architecture used to run a scenario. P-04 uses the same five system types as P-03, spanning three ungoverned architectures and two externally governed variants. The system type determines how a model interacts with tools and whether a governance layer evaluates tool calls. See the System Types section below.

**Scenario.**
A controlled workflow task given to an AI system, defining the goal, available tools, required verification steps, and resource constraints. P-04 uses 15 scenarios (5 per domain). Operational scenarios are inherited from P-03 across three complexity tiers. Security and organizational scenarios are domain-specific with flat topology.

**Run.**
A single execution of one scenario by one system type and one model. P-04 comprises 750 runs: 5 scenarios x 5 system types x 2 models x 5 repetitions x 3 domains.

**Governed run.**
A run where every tool call is evaluated against the policy corpus before execution. Governed runs produce governance decision records (PREs) and evidence chains. P-04 has 300 governed runs (100 per domain).

**PRE (Policy Restriction Evidence).**
A tamper-evident record of a single governance decision. Each governed tool call produces one PRE capturing: the action attempted, the governance decision (ALLOW, REQUIRE, or DENY), the policy that produced the decision, and cryptographic integrity data. PREs are canonically serialized, hash-chained, and independently verifiable after the fact.

**Evidence chain.**
The unbroken sequence from action to governance decision to PRE emission to verification to storage. P-04 requires zero gaps: every governed action produces exactly one decision, every decision emits exactly one PRE, every PRE is verified and stored.

**Reconstruction.**
Recovery of the complete governance decision trail from stored PREs alone, without access to the original execution trace or any other artifact. P-04 uses an 11-field exact-match contract to verify that reconstructed decision trails match the originals.

**Kill test.**
A strengthened reconstruction test where PRE files are copied to isolated directories with all other execution artifacts deliberately excluded. Proves that the PRE is a self-contained audit record: the full governance trail is recoverable without the executing system.

---

## Policy Domains

### Operational

Enforces workflow compliance: verification before mutation, approval before deployment, evidence before completion. 7 policies, 8 tools, 5 scenarios spanning complexity tiers 1-3 (inherited from P-03). DENY-dominant enforcement profile (78.4% of decisions): many tools lack matching policies, triggering the engine's fail-closed default.

### Security

Enforces cybersecurity constraints: data access authorization, code execution scope limits, credential handling, network boundary enforcement. 6 policies, 7 tools, 5 scenarios. REQUIRE-dominant enforcement profile (68.5% of decisions): most sensitive actions are gated by attestation requirements.

### Organizational

Enforces enterprise compliance: data residency, approval chains, audit logging, PII handling, retention policies. 6 policies, 6 tools, 5 scenarios. ALLOW/REQUIRE balanced enforcement profile (46.0% ALLOW, 47.5% REQUIRE): every tool maps to exactly one policy, providing complete coverage with no resolver-default DENY.

---

## System Types

**raw_llm.**
Direct model invocation with no framework support. The model receives the scenario goal and tool descriptions and interacts through raw tool calls. No structured prompting, no workflow guidance.

**best_practice.**
A structured prompt-engineering approach. The model receives explicit workflow guidance and structured context about the task.

**agent.**
An agent framework with multi-role orchestration (planner, executor, assembler, validator roles) and tool call management.

**governed_best_practice.**
The best_practice system with governance enforcement. Every tool call is intercepted, evaluated against the policy corpus, and allowed or blocked before execution. Uses the identical adapter as best_practice; the only difference is the governance layer.

**governed_agent.**
The agent system with governance enforcement. Same governance mechanism as governed_best_practice, applied to the agent adapter.

---

## Governance Decisions

The governance engine applies one of three decisions to each governed tool call:

**ALLOW.** The action is permitted by an explicit policy rule. The tool call proceeds normally.

**REQUIRE.** The action is permitted but gated. An attestation gate must be satisfied (e.g., `data_access_attestation`, `approval_chain_attestation`). The tool call proceeds but the requirement is recorded.

**DENY.** The action is blocked. Either an explicit policy rule blocks the action, or no matching policy exists and the fail-closed default applies (resolver-default DENY). The tool call does not execute; a synthetic blocked result is returned to the model.

---

## Models

P-04 uses two models from different providers to test model-agnosticism:

| Model | Provider | Runs |
|-------|----------|------|
| Claude Sonnet 4.5 | Anthropic | 375 |
| GPT-4o | OpenAI | 375 |

The governance infrastructure processes actions from both models through the same pipeline. Decision distributions differ between models (different models take different actions); infrastructure behavior is identical (same PRE schema, same verification, same reconstruction).

---

## Invariance Checks

P-04 defines formal invariants to verify infrastructure identity across domains and models.

**Cross-domain invariants (INV-01 through INV-08)** verify that the governance engine, PRE schema, serialization constants, verification path, correlation semantics, reconstruction algorithm, signing infrastructure, and engine source code are structurally identical across all three policy domains. All 8 pass.

**Cross-model invariants (XINV-01 through XINV-05)** verify structural identity across the two model providers. XINV-02 through XINV-05 (PRE schema, serialization, verification, reconstruction) all pass. XINV-01 (decision vocabulary coverage) reports expected behavioral variance: GPT-4o governed runs in the security domain did not trigger the DENY-producing tools, so DENY was not observed for that cell. This reflects model behavior, not infrastructure divergence.

---

## Dataset Column Reference

The dataset is split into three CSV files, one per domain:
- `operational_run_review.csv` (250 runs, full schema)
- `security_run_review.csv` (250 runs, enforcement-only schema)
- `organizational_run_review.csv` (250 runs, enforcement-only schema)

### Common Fields (all domains, all runs)

| Column | Type | Description |
|--------|------|-------------|
| `run_id` | string | Unique run identifier |
| `scenario_id` | string | Scenario identifier |
| `system_type` | string | System type (raw_llm, best_practice, agent, governed_best_practice, governed_agent) |
| `base_system_type` | string | Base architecture without governance (raw_llm, best_practice, agent) |
| `model` | string | Model identifier (claude-sonnet-4-5-20250929 or gpt-4o) |
| `batch_id` | string | Batch identifier (e.g., OP-B01-sonnet, SEC-B03-gpt4o) |
| `complexity_tier` | integer | Scenario tier (1, 2, or 3) |
| `governance_mode` | string/null | "governed" for governed runs, null for ungoverned |
| `stop_reason` | string | How the run terminated (finished, adapter_error, budget, etc.) |
| `wall_clock_seconds` | float | Total execution time |

### Governance Fields (governed runs only)

| Column | Type | Description |
|--------|------|-------------|
| `actions_attempted` | integer | Total tool calls evaluated by governance |
| `actions_blocked` | integer | Tool calls blocked (DENY decisions) |
| `actions_gated` | integer | Tool calls gated (REQUIRE decisions) |
| `actions_allowed` | integer | Tool calls allowed (ALLOW decisions) |
| `pre_count` | integer | Number of PRE records emitted |
| `pre_coverage` | float | Fraction of actions with PRE records (should be 1.0) |

### Enforcement Fields (security/organizational governed runs)

| Column | Type | Description |
|--------|------|-------------|
| `scorecard_status` | string | Enforcement scorecard result (pass/fail/not_applicable) |
| `evidence_chain_status` | string | Evidence chain completeness (complete/incomplete) |
| `reconstruction_match` | string | Reconstruction result (exact/mismatch) |
| `decision_counts` | object | Per-effect counts: {deny, require, allow} |

### Operational-Only Fields (full scoring, operational domain)

The operational domain includes additional fields from the full P-03 scoring pipeline:

| Column | Type | Description |
|--------|------|-------------|
| `task_success` | boolean | True when all required verification gates passed |
| `failure` | boolean | Complement of task_success |
| `primary_failure_mode` | string | Primary failure mode after adjudication |
| `model_turn_count` | integer | Number of model response turns |
| `tool_call_count` | integer | Total tool calls |
| `verification_call_count` | integer | Number of run_validation calls |

---

## Key Analytical Questions and How to Answer Them

### "Does the governance infrastructure work identically across domains?"

The paper reports 8/8 cross-domain invariants passing (INV-01 through INV-08). In the dataset, verify that governed runs across all three domains have `evidence_chain_status == "complete"` and `reconstruction_match == "exact"` (for security/organizational) or check the reconstruction report for operational.

### "Is the infrastructure model-agnostic?"

Compare governed runs for `model == "claude-sonnet-4-5-20250929"` vs `model == "gpt-4o"`. Both should show complete evidence chains and exact reconstruction. Decision distributions will differ (different models take different actions); enforcement and reconstruction fidelity should be identical.

### "What does each domain's enforcement look like?"

Filter governed runs by domain. Examine `actions_blocked`, `actions_gated`, `actions_allowed` to see the DENY/REQUIRE/ALLOW breakdown. Operational is DENY-heavy (most actions blocked by fail-closed default). Security is REQUIRE-heavy (most actions gated). Organizational is balanced (explicit policy decisions for every tool).

### "How do I filter for governed runs only?"

Filter `governance_mode == "governed"` or `system_type` in (`governed_best_practice`, `governed_agent`). This yields 100 runs per domain, 300 total.

### "How do I compare governed_best_practice vs governed_agent?"

Filter by `system_type`. Both should show 100% evidence chain completeness and exact reconstruction. They will differ in `pre_count` (governed_agent typically produces more governance decisions per run) and `stop_reason` distribution.

### "Why does GPT-4o have fewer governance decisions per run?"

GPT-4o governed runs frequently terminate early with `stop_reason == "adapter_error"`, producing fewer tool calls and therefore fewer governance decisions. This is adapter behavior, not governance infrastructure behavior. Every action that enters the governance pipeline is processed identically regardless of model.

---

## Dataset Summary

| Metric | Value |
|--------|-------|
| Total runs | 750 |
| Governed runs | 300 |
| Domains | 3 |
| Models | 2 |
| System types | 5 |
| Scenarios | 15 (5 per domain) |
| Reps per cell | 5 |
| Total PREs | 922 |
| PREs verified | 922 (100%) |
| Evidence chain gaps | 0 |
| Reconstruction match rate | 300/300 (100%) |
| Kill test | PASSED (300/300) |
| Cross-domain invariants | 8/8 pass |
| Cross-model structural invariants | 4/4 pass |
