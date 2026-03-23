> **Archived 2026-03-23.** Superseded by [EvoIntel White Paper v3.2](../../README.md). This document was the internal planning scaffold that led to the whitepaper. Retained for historical context.

# EvoIntel Unified Thesis 2026

Generated: 2026-03-02

## 1) Purpose

This document merges and updates:
- `EvoIntel_MCP_Suite_Thesis.md`
- `EvoIntel_Next_Gen_Stack.md`
- `EvoIntel_Security_Debugging_Research.md`

It is a reality-based source of truth: what is already built, what is still missing, and what we should build next without unnecessary platform churn.

## 2) Core Thesis

AI coding agents are not primarily limited by generation quality. They are limited by missing visibility into project history, runtime behavior, cross-service contracts, quality risk, and web context quality. EvoIntel exists to close these visibility gaps with local sidecars exposed via MCP.

The suite goal is verification intelligence, not tool sprawl.

## 3) Naming + Identity Rules

- Matrix naming theme applies to core sidecars: `Sentinel`, `Niobe`, `Seraph`, `Merovingian`.
- `Anno` is an explicit exception: pre-theme, already launched, and should remain stable as-is.
- Do not rename repos/packages for branding alone unless there is a clear product-level migration plan with compatibility shims.

## 4) Ground Truth (Codebase Audit)

Audit scope:
- `/home/evo-nirvana/dev/projects/sentinel`
- `/home/evo-nirvana/dev/projects/niobe`
- `/home/evo-nirvana/dev/projects/seraph`
- `/home/evo-nirvana/dev/projects/merovingian`
- `/home/evo-nirvana/dev/projects/anno`

### Current implementations

| Blind Spot | Tool | Version | Current State |
|---|---|---:|---|
| Project history + institutional memory | Sentinel | 0.4.0 | Mature MCP + CLI; conventions, pitfalls, decisions, hot files, co-change, feedback, solution memory |
| Runtime behavior | Niobe | 0.2.0 | Snapshot/compare model, log ingestion, anomaly detection, MCP audit + feedback |
| Cross-repo contracts | Merovingian | 0.1.0 | OpenAPI/Pydantic scanning, consumer mapping, breaking and impact analysis |
| Code quality/risk gate | Seraph | 0.1.0 | Baseline/flaky detection, mutation testing, static analysis, security analysis, scoring |
| Web extraction | Anno | 1.0.1 | Production-grade HTTP API + MCP proxy + CLI, ensemble extraction, broad route surface |

### MCP tool reality (from code)

- Sentinel MCP tools: 11
  - includes `sentinel_solution_save`, `sentinel_solution_search`, `sentinel_solution_verify`
- Niobe MCP tools: 8
- Merovingian MCP tools: 8
- Seraph MCP tools: 4
- Anno MCP tools: 4 (`anno_fetch`, `anno_batch_fetch`, `anno_crawl`, `anno_health`)

### What is already done (important corrections)

From the research docs, the following items are already implemented and should no longer be treated as future work:

1. Sentinel solution memory is implemented (schema + tools).
2. Seraph security analysis exists (Bandit/Semgrep paths in code and scoring flow).
3. Niobe anomaly baseline logic is implemented (rolling baseline + thresholding).

## 5) Gap Map (Reality vs Vision)

The biggest remaining gaps are integration and orchestration, not raw feature count.

### Gap A: No unified verdict surface

Today, each sidecar is strong in isolation. There is no single MCP endpoint that returns a combined pre-merge risk verdict across code quality, contract impact, historical risk, and runtime confidence.

Impact:
- Higher cognitive load for agents and humans.
- Inconsistent merge decisions across repos.

### Gap B: Limited cross-tool feedback loops

Examples not fully connected yet:
- Niobe runtime regressions feeding back into Sentinel/Seraph risk priors.
- Merovingian contract deltas linked to Sentinel co-change and incident history.
- Seraph score calibration with post-deploy outcomes.

Impact:
- Intelligence remains local instead of compounding over time.

### Gap C: Merovingian phase-2 intelligence

Not yet visible in codebase:
- Contract drift detection (spec vs observed runtime behavior).
- Cross-repo co-change prediction linked to breakage likelihood.

Impact:
- Strong static contract checks, weaker dynamic contract confidence.

### Gap E: Agent Authentication Blindness

AI agents cannot authenticate to the platforms they need to act on. Want an agent to post a status update, file an issue, or check a dashboard? The developer is copy-pasting credentials into plaintext files or manually running commands on the agent's behalf.

The infrastructure for agent identity does not exist yet:
- Scoped tokens (post-only, read-only, time-limited)
- Encrypted credential vaults accessible to agents
- Auditable access logs per agent action
- Platform-side agent identity verification

OAuth solved this for web apps twenty years ago. Nobody has solved it for agents. This is not a model problem — it is a plumbing problem, and it is solvable within the EvoIntel architectural pattern (local sidecar, MCP-exposed, no cloud required).

Impact:
- Agents remain human-dependent for any action requiring authentication
- Credentials stored insecurely or not at all
- No audit trail for agent-initiated platform actions

Note: EvoIntel does not plan to build this in the current roadmap. It is documented here as an identified blindness to frame future work or ecosystem opportunity.

### Gap D: Governance artifacts are thin

Even with good analysis, policy artifacts are not yet standardized:
- Unified verification report schema across tools.
- Explicit go/no-go policy language for CI and AI-assisted merge.

Impact:
- Harder to institutionalize safe automation.

## 6) Recommended Roadmap (Realistic + Future-Proof)

## Phase 1 (Now): Documentation + Contracts First

Goal: stabilize strategy before architecture changes.

Deliverables:
1. This unified thesis as source-of-truth.
2. Canonical verification report schema (`grade`, `blocking_issues`, `confidence`, `evidence_refs`).
3. Tool boundary contract doc:
   - Anno remains standalone launched product.
   - Matrix core sidecars remain independent but interoperable.

Why first:
- Low risk.
- Prevents costly churn from premature renames/restructures.

## Phase 2 (Next): Verdict Orchestrator (Thin Integration Layer)

Goal: one entrypoint for merge-time intelligence.

Proposed project: `Oracle` (PyPI: `oracle-mcp`) — thin orchestration layer, do not absorb existing tools.

The Oracle synthesizes intelligence from all four core sidecars and returns a single verdict. Named after the Matrix character who aggregates signals from all agents and tells you what is real.

Scope:
1. `oracle_assess(repo_path, diff_ref?)`
   - Reads Seraph assessment.
   - Reads Sentinel risk signals.
   - Reads Merovingian impact for changed services/contracts.
   - Optionally annotates with latest Niobe anomalies for touched services.
2. Returns both:
   - machine JSON (stable schema)
   - compact markdown (human review)
3. Starts as read-only orchestrator (no write-back side effects).
4. Individual tools remain independent — Oracle is the conductor, not the replacement.

Why thin-layer first:
- Minimal coupling.
- Easy rollback.
- Allows incremental confidence tuning.

### Use Case: Session Health Check

One gap the individual tools don't close: a new coding session starts cold. Claude Code has no awareness of what it should do *before* writing a single line of code. Each tool is available, but nothing tells the agent to invoke them.

Oracle's first useful capability is a `oracle_session_start(repo_path)` call that replaces the multi-step ritual of:
1. `sentinel_project_context` — prime with conventions and pitfalls
2. `niobe_snapshot` — establish a performance baseline before changes
3. `merovingian_graph(repo_name)` — understand which upstream/downstream contracts are in play
4. `sentinel_hot_files` — know which files to approach carefully

That sequence is already possible today if the developer knows to do it. Oracle would expose it as a single entrypoint so the AI doesn't skip steps. A CLAUDE.md instruction (`"At session start, call oracle_session_start"`) replaces four separate tool-awareness requirements with one. The individual tools remain independent — Oracle is purely additive.

## Phase 3: Closed-Loop Calibration

Goal: move from static verdicts to calibrated verdicts.

Additions:
1. Runtime outcome linker:
   - map deployed change -> Niobe signals (regression/no regression)
2. Calibration store:
   - track predicted risk vs observed outcome
3. Weight tuning:
   - adjust verdict weighting by empirical false positives/negatives

Result:
- Verification quality improves with every release cycle.

## Phase 4: Policy + Enterprise Readiness

Goal: safe automation with auditable governance.

Deliverables:
1. Policy gates (example):
   - block if critical security findings > 0
   - block if breaking consumer impact unresolved
   - block if runtime regression confidence above threshold
2. Signed audit trail:
   - verdict id, tool evidence refs, decision, approver
3. Compliance-oriented report exports (SOC2/PCI evidence mapping)

## 7) Design Constraints (Do Not Violate)

1. Avoid tool bloat.
   - No Halcyon-style 20+ tool explosions.
2. Keep sidecars separable.
   - Avoid tight compile-time coupling across all repos.
3. Preserve zero/low-infra local-first operation.
   - SQLite + MCP remains default.
4. Keep LLM output contracts concise.
   - pagination, truncation, high-signal summaries.
5. Keep backward compatibility for launched surfaces.
   - especially `Anno` API/CLI/MCP behavior.

## 8) Practical Upgrade Backlog (Ordered)

1. Define `Oracle` report schema in markdown + JSON examples.
2. Build `Oracle` MVP as read-only orchestrator (`pip install oracle-mcp`).
3. Add evidence references (tool, record id, timestamp) in oracle output.
4. Add calibration ingestion from Niobe outcomes.
5. Add CI policy adapter (`pass/warn/block`) from oracle output.
6. Add compliance export profiles (SOC2/PCI friendly evidence bundles).

## 9) Decision Summary

- Do not do massive renames right now.
- Do not touch Anno naming/product boundaries.
- Build forward through integration (`Oracle`) and calibration loops.
- Keep Matrix theme in core tool identity while prioritizing operational stability.

