# EvoIntel: Verification Infrastructure for AI-Native Development

**Version 4.0 | March 2026 | Evolving Intelligence AI**

**Author:** Nicholas Smith — AI Innovator, ServiceNow Developer, Software Designer. Founder & CEO of Evolving Intelligence AI.

[GitHub](https://github.com/evo-hydra) | [LinkedIn](https://www.linkedin.com/in/nicholas-smith-90466a12) | [Evolving Intelligence AI](https://www.linkedin.com/company/evolving-intelligence-ai/) | [evolvingintelligence.ai](https://www.evolvingintelligence.ai)

---

## The Thesis

AI coding agents are not limited by intelligence. They are limited by blindness.

They cannot read institutional memory. They cannot observe runtime behavior. They cannot see cross-service blast radius. They cannot distinguish tests that verify correctness from tests that verify execution. They cannot act on the web.

No model improvement fixes any of these. Bigger context windows don't help when the information lives in thousands of commits, running processes, downstream consumers, mutation survival rates, and pages behind Cloudflare challenges. The gap between what AI can *think* and what it can *see* is growing, not shrinking.

But blindness is only half the problem.

Even when agents can see — when you give them the right tools, the right context, the right instructions — they skip the check. They assert "FDMC: Consistent — matched existing pattern" without reading a sibling file. They claim they checked conventions without running a grep. Under context pressure, they optimize for throughput and drop the slow verification steps that happen to be the most valuable.

You cannot fix a behavioral problem with a better prompt. You fix it with a gate that won't open until the agent shows its work.

**EvoIntel is verification infrastructure for AI-assisted development.** Six local MCP sidecars that give agents sight. A quality standard that encodes the judgment models lack. An enforcement layer that requires evidence at every gate. Together they form the missing layer between model intelligence and trustworthy autonomous software work.

The industry is solving for smarter agents. The harder problem is agents you can trust.

---

## Part I: The Five Blindnesses

AI coding agents face five structural gaps that no model improvement will fix:

**1. Project History** — AI can read files. It cannot read institutional memory. Why was that workaround added? Which files always break together? What conventions are actually followed? This intelligence lives in thousands of commits, PR descriptions, and co-change patterns — outside any context window.

**2. Runtime Behavior** — AI can reason about what code should do. It cannot observe what it actually does. CPU spikes, memory leaks, error rates, log anomalies — these require process observation, not code reading.

**3. Cross-Service Dependencies** — Change an API field in one service and you silently break downstream consumers. No local test catches it. No linter flags it. The blast radius extends beyond the AI's field of vision.

**4. Code Quality Beyond "Tests Pass"** — AI-generated tests can achieve 100% line coverage while scoring only 4% on mutation testing. They assert that code runs without errors, not that it produces correct results.

**5. Web Autonomy** — AI agents cannot act on the web. Raw HTML wastes ~93% of tokens on noise. And even when agents get clean content, they lack the ability to navigate, authenticate, interact with pages, or maintain browser sessions. The web is the largest information surface in the world, and AI agents are locked out of it.

### The Numbers

The scale of the problem: 45% of AI-generated code contains CWE vulnerabilities (Veracode 2025). AI-generated tests achieve 100% line coverage but only 4% mutation scores — they verify that code *runs*, not that it's *correct*. Refactoring has collapsed from 25% to under 10% of code changes (GitClear 2025), replaced by copy-paste.

Model improvement addresses reasoning. Blindness requires infrastructure.

---

## Part II: The Infrastructure

### Architecture: Local Sidecar Intelligence

Every tool in EvoIntel follows the same pattern:

1. Identify what the AI literally cannot see
2. Build a local sidecar that extracts intelligence from that domain
3. Persist it locally — SQLite, WAL mode, FTS5 search. No cloud, no Docker
4. Expose it via MCP so the agent can query it like any other tool

The AI doesn't get smarter. It gets informed.

**6 servers. 54 MCP interfaces. 4,036 tests. Open source.**

### The Core Triangle

Three tools form the load-bearing structure. Everything else extends from these.

**[Sentinel](https://github.com/evo-hydra/sentinel)** (v0.4.2, 418 tests, 12 MCP tools) — **Institutional memory.** Extracts from git history: conventions with confidence scores, pitfalls from reverts and bug fixes, architectural decisions with rationale, hot files by churn and fragility, co-change pairs that historically move together, and solution memory linking error fingerprints to fixes (searchable via FTS5). Health checks perform periodic whole-project sweeps — version consistency, commit deltas, test count regression, dead imports. The feedback loop is critical: agents submit accepted/rejected/modified on entries, recalibrating confidence. Over time, Sentinel learns which conventions are actually followed.

**[Seraph](https://github.com/evo-hydra/seraph)** (v0.1.2, 201 tests, 4 MCP tools) — **Verification beyond tests.** 8-step pipeline: diff → baseline (flaky detection) → mutation testing (mutmut) → static analysis (ruff + mypy) → security scanning (bandit + semgrep + detect-secrets) → Sentinel risk signals → scoring → persistence. Six-dimension grade: mutation score (25%), static cleanliness (20%), test baseline (10%), Sentinel risk (20%), co-change coverage (10%), security (15%, CWE-tier weighted). *Dimension weights are initial heuristics, not empirically calibrated against defect rates. An A grade means "the formula liked the code" — it does not guarantee the code is safe to ship.*

**[Morpheus](https://github.com/evo-hydra/morpheus-mcp)** (v0.3.0, 204 tests, 8 MCP tools) — **Protocol enforcement.** Tracks plan state in SQLite and requires evidence at each phase gate. Agents cannot advance past CODE without proving they read a sibling file. They cannot advance past COMMIT without a Seraph assessment ID. They cannot skip phases. Task size tiers (micro/small/medium/large) adjust gate strictness — a 3-line fix doesn't get the same ceremony as a 200-line subsystem. The oil change gate rejects the first task of any plan when commits since the last health check exceed the configured threshold.

### The Extensions

Three additional sidecars address specific blindnesses:

**[Niobe](https://github.com/evo-hydra/niobe)** (v0.2.1, 145 tests, 8 MCP tools) — **Runtime observation.** Snapshot-based: register a service, capture metrics via psutil, ingest logs, make a change, snapshot again, compare. Anomaly detection after 3+ baselines flags metrics exceeding mean + 2σ.

**[Merovingian](https://github.com/evo-hydra/merovingian)** (v0.1.4, 200 tests, 10 MCP tools) — **Contract intelligence.** Scans OpenAPI specs and Pydantic models. Direction-aware breaking change detection. Consumer registry creates dependency graph. Impact analysis maps blast radius across repos.

**[Anno](https://github.com/evo-hydra/anno)** (v2.0.0, 2,868 tests, 12 MCP tools) — **Web autonomy.** Gives AI agents a body on the internet. Navigate, authenticate (Cloudflare challenge solving), interact (click, fill, scroll), observe (page type classification, pattern detection), extract (five parallel extractors, 92.7% average token reduction), and monitor — through a stealth browser with persistent sessions. 12 MCP tools including `anno_interact`, `anno_observe`, `anno_screenshot`, `anno_workflow`, and `anno_session_auth`. Anno is the only tool in the suite that *acts* — everything else thinks.

| Blindness | Tool | Tests | MCP Tools | What It Sees |
|-----------|------|-------|-----------|-------------|
| Project history | **Sentinel** | 418 | 12 | Conventions, pitfalls, decisions, hot files, co-changes, solution memory, health checks |
| Runtime behavior | **Niobe** | 145 | 8 | Process metrics, log patterns, error rates, anomalies |
| Cross-service deps | **Merovingian** | 200 | 10 | API contracts, consumers, breaking changes, blast radius |
| Code quality | **Seraph** | 201 | 4 | Mutation survival, static analysis, flakiness, security, risk scoring |
| Web autonomy | **Anno** | 2,868 | 12 | Navigate, authenticate, interact, observe, extract, monitor |
| Protocol enforcement | **Morpheus** | 204 | 8 | Plan state, phase gates, evidence validation, oil change enforcement |

### Shared Engineering DNA

| Layer | Implementation |
|-------|---------------|
| Storage | SQLite + WAL + FTS5. Database at `.tool-name/tool-name.db` |
| Transport | FastMCP over stdio (except Anno: HTTP) |
| Config | `.tool-name/config.toml` → env vars → frozen dataclass defaults |
| CLI | Typer + Rich on stderr (MCP-safe) |
| Output | Markdown, paginated, capped ~4,000 tokens |
| Build | Hatchling, `src/` layout, optional MCP extra |

---

## Part III: FDMC — The Quality Standard

FDMC encodes the judgment that models lack. Four lenses, applied as a single post-code review pass:

- **Future-Proof** — Will this break when requirements change? Am I baking in assumptions about callers, data shapes, or execution order?
- **Dynamic** — Am I hardcoding something that should be configurable? Magic numbers, paths, thresholds — should these be config?
- **Modular** — Does this have one clear responsibility? If I can't describe it in one sentence, split it.
- **Consistent** — Does this follow existing patterns in the codebase? Match what's already there before inventing something new. **This is the most commonly violated lens.**

**Origin**: FDMC is not a framework derived from theory. It is the mental discipline the author developed as a working programmer — the four questions he asked himself on every code change, across every project, before AI agents existed. The four lenses map to known principles (Open/Closed, twelve-factor, SRP, style consistency). The novelty is not the lenses — it is the enforcement mechanism and the application context: autonomous agents that skip checks humans wouldn't.

### Why Four Lenses, Not Five

Observability is the obvious candidate for a fifth lens. The exclusion is deliberate: FDMC's four lenses are all **active at write-time** — structural decisions made during coding. Observability is **passive after the fact** — it tells you what happened, not how to structure what you're building. Observability belongs in the feedback layer (Niobe, health checks, oil changes), not in the structural standard. *a, e, i, o, u — and sometimes y.*

### From Checklist to Enforced Gate

FDMC v1 was "read these bullet points before coding." It was ignored.

FDMC v2 added checkpoints with red flags. The agent rubber-stamped them. During the hex-engine case study, the agent wrote `FDMC: Consistent — matched existing pattern` without ever reading a sibling file.

FDMC v3 made it an **enforced gate** via Morpheus. The agent must submit the file path of the sibling it read as evidence. The server rejects empty or missing evidence. An independent `/review` subagent — structurally separate, fresh context window, no memory of writing the code, no sunk-cost pressure — reviews against all four lenses and returns a structured verdict. Structural violations must be fixed before grading.

| Lens | Red Flag |
|------|----------|
| Future-Proof | Struct fields that duplicate existing types. APIs that assume a specific caller. |
| Dynamic | Magic numbers, embedded strings, hardcoded paths. |
| Modular | Function doing parsing AND validation AND storage. |
| Consistent | New standalone class when siblings are manager-owned. Different integration pattern. |

---

## Part IV: The Dev Loop — Prove, Don't Claim

### The Missing Layer

The MCP tools are sensors. FDMC is the quality standard. Neither tells the agent *when* to look, *what* to look at, or *how* to respond. And even when told, the agent skips steps under context pressure.

The Dev Loop is the orchestration protocol. Morpheus is the enforcement layer.

```
BOOTSTRAP (once per plan)
    |
    v
+-- CHECK --> CODE --> TEST --> /REVIEW --> GRADE --> COMMIT --> ADVANCE --+
|                                                                          |
+------------------------------- next task --------------------------------+
    |
    v
CLOSE (once per plan)
```

### Evidence Gates

This is the core innovation. Each phase requires evidence submission to Morpheus. The server validates and rejects empty claims.

| Phase | Required Evidence |
|-------|-------------------|
| CHECK | *(entry point)* |
| CODE | `sibling_read` — file path of the sibling read for Consistent check |
| TEST | Build verification output |
| GRADE | Test results + `fdmc_review` — post-code FDMC one-liner |
| COMMIT | Seraph assessment ID, `"seraph_unavailable"`, or `skip_reason` |
| ADVANCE | Knowledge: solution saved, solution verified, `"nothing_surprised"` + reason |

### Adaptive Protocol

The 6-phase ceremony is not applied uniformly:

- **MICRO** tasks skip all gates — zero ceremony for 3-line fixes
- **SMALL** tasks skip FDMC review, Seraph, and knowledge gates — lightweight pass-through
- **MEDIUM** tasks (default) get the full protocol
- **LARGE** tasks enforce Seraph grading even when `grade: false`
- **Greenfield mode** relaxes `sibling_read` when all files are new
- **Batch advance** processes multiple phases in a single MCP call, cutting 80%+ overhead for small tasks

### The Oil Change: Micro Lens vs Macro Lens

The Dev Loop operates at the **micro lens** — task-level verification. Each task reads a sibling, runs tests, gets graded and reviewed. This catches local violations.

But there is a class of bugs the micro lens is structurally incapable of seeing. Architectural drift — individually correct changes that collectively diverge. Convention erosion — patterns followed in commits 1-50 violated in commits 51-100, with no single diff showing the break. Dead code from incomplete refactors. Accidental parallelism across tasks.

**The oil change pattern**: After every N commits (configurable, default 40), the protocol requires a full-project sweep — not a task-level review, but a macro-lens pass across all files, all patterns, all conventions. Morpheus enforces the interval: `morpheus_advance` rejects the first task of any plan until the oil change is recorded. `sentinel_health_check` captures the data. The micro lens asks "is this task correct?" The macro lens asks "is this project still healthy?" Both are necessary. Neither is sufficient alone.

During dogfood testing, a cross-model oil change (GPT reviewing Claude's code) found three show-stopping bugs that had passed every task-level gate — every sibling read, every Seraph assessment, every `/review` verdict. The bugs were macro-lens bugs: patterns that only emerge across 30+ commits, not within any single diff.

### Multi-Repo: Hub-and-Spoke

MCP tools are designed for single-repo contexts. When work spans multiple repos, a hub agent plans holistically at the parent level, then spawns spoke agents that execute in per-repo contexts with correct CWD. Each spoke gets the natural environment its tools expect. The hub collects results, runs feedback sweeps, coordinates. Principle: **plan holistically, execute locally.**

### Why MCP Matters

Because every tool is an MCP server — not a plugin, not a prompt, not a framework hook — any agent from any framework connects and gets governed by the same gates. Claude Code, a CI bot, an Agent SDK deployment, a competitor's framework — same server, same protocol, same evidence. The quality standard is portable because it's infrastructure, not instructions.

---

## Part V: Evidence

*Scope caveat: All case studies are solo-developer projects under 100k LOC — no team dynamics, no legacy debt, no compliance requirements. The protocol has proven itself on controlled, greenfield-to-early-stage codebases. Whether it scales to enterprise environments is unvalidated. The architecture is designed to scale technically, but protocol overhead, gate calibration, and feedback dynamics at scale remain open questions.*

### hex-engine: The Agent That Violated Its Own Rules (March 14, 2026)

The Dev Loop v1 ran against hex-engine, a C++ strategy game engine with 25+ test suites and 70+ source files.

**Results**: 12/12 tasks completed. 11 commits. 415 tests passing. ~4,800 lines added.

**What broke**: The agent used 4 of 32 available MCP tools. It called 4 Sentinel tools per task — all returned "not initialized." It called `seraph_assess` once with `skip_mutations=true`, producing a meaningless grade. It never submitted feedback to any server.

**The FDMC violations were worse**: Task 1 created `hex::ArtifactData` when `hex::game::ArtifactData` already existed — a Consistent lens failure that cascaded into conversion functions across Task 2. Task 7 made `SafetyMetricsTracker` standalone when every other subsystem is owned by `GameManager`. Task 10 repeated the same pattern violation.

The agent that was supposed to apply FDMC didn't. Not because it couldn't — because nothing enforced it.

### morpheus-mcp: The Bootstrap Paradox (March 15, 2026)

The Dev Loop v2 built its own orchestrator. Morpheus was built entirely by the protocol it enforces.

**Results**: 10/10 tasks completed. 11 commits. 81 tests. 88% coverage. Published to PyPI.

**What the protocol caught during its own construction**: The agent created `~/.pypirc` for PyPI authentication while every sibling project uses `.env` files in the project root. The agent that designed the `sibling_read` gate violated the Consistent lens while building that gate.

**Key insight**: You cannot fix a behavioral problem with a better prompt. The agent that *designed* FDMC enforcement *violated* FDMC. This validates the entire thesis — self-critique is insufficient. Enforcement is necessary.

### The Hardening Trajectory (March 19-24, 2026)

Five hardening rounds (R1-R5) drove the protocol from v3 to v3.9. The pattern was consistent: dogfood → discover what agents skip or game → add enforcement or remove dead weight.

| Round | Tasks | Key Discovery | Fix |
|-------|-------|--------------|-----|
| R1 (v0.2.0) | 20/20 | 6-phase ceremony is dead weight for small tasks | Task size tiers, batch advance, merged FDMC |
| R2 | 7/9 | Morpheus crashed tracking its own plan (`"None is not a valid TaskSize"`) | Defensive store parsing |
| R3 | 9/9 | Morpheus crashed *again* (`"fromisoformat: argument must be str"`) — different bug, same class | Comprehensive NULL defense across all row parsers |
| R4 | 7/7 | Cross-model oil change found scoring integrity bug: failed dimensions scored as perfect | `evaluated` set starts empty; added only after success |
| R5 | 12 tasks | `SELECT *` column order broke SMALL gate — every SMALL task silently read as MEDIUM | Explicit column lists in SQL |

**The cautionary tale from R3**: Three consecutive runs where Morpheus crashed, three runs where the plan added features on top of the crash instead of fixing it. The protocol's own fallback (prompt-only enforcement) was so effective it masked the urgency of the underlying failure. The fix was to step outside the framework: no plan file, no gates. Just read the store, find every unsafe parse, fix them all. The lesson: when the enforcement infrastructure is the thing that's broken, enforcing your way to a fix is circular.

**Cumulative**: 4,036 tests across 6 tools. Zero regressions across 5 hardening rounds.

---

## Part VI: Why This Matters Now

Models are improving faster than verification infrastructure. Every capability jump — longer context, better reasoning, tool use, multi-step planning — makes agents more autonomous. None of it makes them more trustworthy. The bottleneck has shifted. It is no longer generation. It is trust.

The cost of a wrong line of code written by a human is bounded — someone reviews it, CI catches it, it ships slowly. The cost of a wrong line of code written by an autonomous agent is unbounded — it passes its own tests, reviews its own quality, commits its own work, and moves to the next task. Silent failure compounds at the speed of automation.

The industry response has been to make agents smarter. Better prompts. Better reasoning. Better models. This addresses the wrong constraint. The agent that built Morpheus — the enforcement system for FDMC quality checks — violated FDMC while building it. Not because it lacked intelligence. Because nothing forced it to show its work.

The missing layer is not intelligence. It is verification infrastructure — sensors that see what agents cannot, standards that encode judgment agents lack, and gates that require proof instead of accepting claims.

> *External validation:* Boris Cherny, creator of Claude Code, describes verification loops as "probably the most important thing" — estimating they increase output quality by 2–3×. This aligns with what EvoIntel's dogfood runs demonstrate: enforcement infrastructure is the primary multiplier of agent quality, not model intelligence.

That is what EvoIntel is. Local. Open source. Framework-agnostic. Built to make autonomous software work trustworthy, not just fast.

---

## Part VII: Honest Gaps

### What Works

- Evidence gates prevent the #1 violation (Consistent lens failures)
- Task size tiers eliminate ceremony on small work
- Sentinel co-changes are correct every time in dogfood runs
- The oil change pattern catches what task-level gates miss
- Cross-model review finds what same-model review doesn't
- Prompt-only fallback works when Morpheus is down

### What Doesn't Yet

**No unified verdict surface** — Each sidecar is strong in isolation. No single endpoint returns a combined pre-merge risk verdict. Morpheus absorbs the orchestration role, but a dedicated `morpheus_verdict` tool is the natural next step.

**Seraph weights are heuristics** — Not empirically calibrated against defect rates. Closed-loop calibration (mapping grades to observed outcomes via Niobe) is on the roadmap.

**Adversarial resilience** — The suite was designed for cooperative conditions. Sentinel's persistent memory is simultaneously the learning mechanism AND a potential poisoning surface. The Anno → Sentinel attack chain (web content → extraction → agent context → persistent memory) is a confused-deputy problem that requires provenance tagging and taint tracking to address.

**Enterprise scale is unvalidated** — Local-first means no shared intelligence across teams. For solo developers this is a feature. For enterprise adoption, a shared layer is needed.

**Spec-first layer missing** — Complex features would benefit from a spec document before task decomposition.

---

## Part VIII: Roadmap

### Done (Q1 2026)

Six MCP servers built and dogfooded. 54 MCP interfaces. 4,036 tests. Morpheus adaptive protocol with task size tiers, greenfield mode, batch advance, oil change enforcement. Seraph security dimension, CWE-78 allowlisting, scoring integrity fixes. Sentinel health checks, confidence qualifiers, project_root on all tools. Merovingian auto-relevance, schema fidelity. Niobe partial failure transparency. Anno v2.0.0 with 12 MCP tools. `/review` subagent. Hub-and-spoke multi-repo dispatch. Five hardening rounds. Published to PyPI.

### Now: Validation (Q2 2026)

- Enable full Seraph grading with mutations on real projects
- Dogfood `/review` subagent end-to-end
- Publish dev-loop plugin to Claude Code marketplace
- `morpheus_verdict` — unified pre-merge risk surface

### Next: Enterprise + Calibration (Q3-Q4 2026)

- Closed-loop calibration: Seraph grades → Niobe runtime outcomes
- Cross-sidecar wiring: Niobe anomalies → Sentinel pitfalls, Merovingian breaking changes → Morpheus gates
- Compliance exports (PCI DSS, SOC 2, SSDF)
- Signed audit trails

### Horizon: Platform (2027)

- Agent authentication infrastructure
- SaaS offering for teams
- Cross-repo co-change prediction

---

## Installation

**Quick start:**

1. Enable the **Morpheus** plugin from the Claude Code plugin marketplace
2. Run `/morpheus:setup` — installs all five MCP servers and configures `~/.claude/.mcp.json`
3. Restart Claude Code

Three steps. `/morpheus:setup` is idempotent — run it again to check or repair.

**Manual install:**

```bash
pipx install git-sentinel       # Project intelligence
pipx install niobe              # Runtime observation
pipx install merovingian        # Dependency intelligence
pipx install seraph-ai          # Verification intelligence
pipx install morpheus-mcp       # Protocol enforcement
npm install -g @evointel/anno   # Web autonomy
```

**First plan:** `/morpheus:plan` → `/morpheus:implement`

Source: [github.com/evo-hydra](https://github.com/evo-hydra) | [evolvingintelligence.ai](https://www.evolvingintelligence.ai)

---

## Appendix A: Competitive Landscape

*Methodology caveat: This table was compiled by the author. Tool versions assessed from public documentation, not hands-on testing of every tool.*

| Capability | Aider | Cline | Kiro | Open SWE | Ruflo | EvoIntel |
|-----------|-------|-------|------|----------|-------|----------|
| Lint-test-fix loop | **Yes** | **Yes** | Hooks | No | No | Yes |
| Plan-first workflow | No | **Yes** | **Yes** | **Yes** | **Yes** | Yes |
| Separate reviewer | No | No | No | **Yes** | **Yes** | **Yes** |
| Pre-flight intelligence | No | No | No | No | No | **Yes** |
| Mutation testing gate | No | No | No | No | No | **Yes** |
| Runtime observation | No | No | No | No | No | **Yes** |
| Contract verification | No | No | No | No | No | **Yes** |
| Security scanning | No | No | No | No | No | **Yes** |
| Enforced quality gates | No | No | No | No | No | **Yes** |
| Feedback loop closure | No | No | No | No | No | **Yes** |
| Knowledge persistence | No | Logs | No | No | **Yes** | **Yes** |
| Multi-agent parallelism | No | No | No | **Yes** | **Yes** | No |

## Appendix B: Protocol Evolution

| Version | Trigger | Key Change |
|---------|---------|-----------|
| v1 → v2 | hex-engine failures | Bootstrap-once, single-call context, per-commit Seraph, feedback sweep |
| v2 → v3 | morpheus-mcp build | SQLite-backed state, evidence gates, brain/nervous-system split |
| v3 → v3.2 | 25-task greenfield | Task size tiers, greenfield mode, batch advance, merged FDMC |
| v3.2 → v3.3 | Anno repositioning | 7 new MCP tools, persistent sessions, page comprehension |
| v3.3 → v3.4 | Self-assessment | `/review` subagent, honest caveats throughout |
| v3.4 → v3.5 | Ceremony dogfood | Sentinel project_root, adaptive knowledge gate, seraph_unavailable |
| v3.5 → v3.7 | R2 hardening | Defensive store parsing, health checks, confidence qualifiers |
| v3.7 → v3.8 | Macro-lens discovery | Oil change pattern, MICRO tier, CWE-78 filtering, self-test |
| v3.8 → v3.9 | Cross-model oil change | Scoring integrity, schema fidelity, hub-and-spoke, SELECT * fix |

## Appendix C: Convergence with Industry Research

EvoIntel was built without knowledge of Anthropic's internal research on agentic coding challenges. A March 2026 review revealed independent convergence on the same problems:

| Industry Research Identifies | EvoIntel Addresses |
|---------------------|-------------------|
| **Delegation gap** — 60% usage but only 0-20% full delegation (Anthropic 2026) | Seraph + FDMC + Morpheus — trust through verification |
| **Quality at scale** — automated review is "essential" (Anthropic 2026) | Seraph mutation testing + Morpheus gate enforcement |
| **Context rot** — accuracy degrades as context grows (Anthropic 2026) | MCP sidecars — pre-computed intelligence, ~4,000 token caps |
| **Long-horizon coherence** — agents can't maintain state across sessions | Sentinel solution memory (FTS5) + Morpheus plan state (SQLite) |
| **Property-based testing** — conventional testing can't exhaustively cover programs (Anthropic NeurIPS 2025) | Seraph mutation testing as quality gate |
| **Post-deployment monitoring** — new infrastructure needed (Anthropic 2026) | Niobe runtime observation |

## Appendix D: Design Constraints

1. **No tool bloat** — Six focused tools, each with one responsibility
2. **Separable sidecars** — No tight coupling. Each tool works independently
3. **Local-first** — SQLite + MCP. No cloud, no Docker. *Trade-off: no shared intelligence across teams — a constraint the roadmap addresses*
4. **Concise output** — ~4,000 token caps. High signal per token
5. **Brain + nervous system** — Protocol logic in the skill (brain). State and enforcement in MCP servers (nervous system). Any framework connects

## Appendix E: References

### Empirical Research
- [Veracode 2025 GenAI Code Security Report](https://www.veracode.com/resources/analyst-reports/2025-genai-code-security-report/)
- [Georgetown CSET — Cybersecurity Risks of AI-Generated Code](https://cset.georgetown.edu/publication/cybersecurity-risks-of-ai-generated-code/)
- [CrowdStrike — Hidden Vulnerabilities in AI-Coded Software](https://www.crowdstrike.com/en-us/blog/crowdstrike-researchers-identify-hidden-vulnerabilities-ai-coded-software/)
- [GitClear AI Code Quality 2025](https://www.gitclear.com/ai_assistant_code_quality_2025_research)
- [Qodo State of AI Code Quality 2025](https://www.qodo.ai/reports/state-of-ai-code-quality/)
- [Mutation testing vs AI coverage (TwoCents)](https://www.twocents.software/blog/how-to-test-ai-generated-code-the-right-way/)

### Anthropic Research
- [2026 Agentic Coding Trends Report](https://resources.anthropic.com/hubfs/2026%20Agentic%20Coding%20Trends%20Report.pdf)
- [Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [Building Effective AI Agents](https://www.anthropic.com/research/building-effective-agents)
- [Measuring AI Agent Autonomy in Practice](https://www.anthropic.com/research/measuring-agent-autonomy)

### Practitioner Insights
- [Boris Cherny — How the Creator of Claude Code Uses Claude Code](https://paddo.dev/blog/how-boris-uses-claude-code/) (Emergent Minds, paddo.dev)

### Standards & Industry
- [OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/resource/owasp-top-10-for-llm-applications-2025/)
- [MCP donated to Agentic AI Foundation](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation)
- [AGENTS.md specification](https://agents.md/)

---

*Evolving Intelligence AI — evolvingintelligence.ai*
*"AI coding agents aren't dumb. They're blind. And blindness isn't fixed by smarter neurons. It's fixed by better sensors."*
*"And even when they can see, they skip the check. That's not fixed by better prompts. It's fixed by better gates."*
