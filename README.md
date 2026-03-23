# EvoIntel: Verification Infrastructure for AI-Native Development

**The Five Blindnesses Framework, the Dev Loop Protocol, and the MCP Suite**

Version 3.3 | March 23, 2026 | Evolving Intelligence AI

**Author:** Nicholas Smith — AI Innovator, ServiceNow Developer, Software Designer. Founder & CEO of Evolving Intelligence AI. Built the EvoIntel MCP suite as open-source infrastructure for verified AI-assisted development.

[GitHub](https://github.com/evo-hydra) | [LinkedIn](https://www.linkedin.com/in/nicholas-smith-90466a12) | [Evolving Intelligence AI](https://www.linkedin.com/company/evolving-intelligence-ai/) | [evolvingintelligence.ai](https://www.evolvingintelligence.ai)

---

## Executive Summary

AI coding agents are not limited by intelligence. They are limited by blindness — structural inability to perceive project history, runtime behavior, cross-service dependencies, code quality beyond test passage, and web content buried in HTML noise. No model improvement fixes this. The gap between what AI can think and what it can see is growing, not shrinking.

EvoIntel addresses this with four layers:

1. **The MCP Suite** — Six local sidecar tools (Sentinel, Niobe, Merovingian, Seraph, Anno, Morpheus) that give AI agents sight into what they structurally cannot reach. 50 MCP interfaces. 950+ tests. SQLite + WAL + FTS5. No cloud. No Docker.

2. **FDMC** — A four-lens quality standard (Future-Proof, Dynamic, Modular, Consistent) that encodes the judgment models lack. Applied as a single post-code review pass with **enforced evidence gates** — agents must prove they checked, not just claim they did.

3. **The Dev Loop** — An autonomous development protocol that orchestrates the suite into a coherent cycle: bootstrap MCP servers once, then for each task: check intelligence, code with FDMC, test, self-critique, grade with mutation testing, commit with knowledge persistence, advance. Now **adaptive** — adjusts gate strictness by task size and project maturity. Closes the feedback loop at end of plan.

4. **Morpheus** — An MCP-based orchestration server that tracks plan state with enforced phase gates. The Dev Loop skill is the brain (protocol in the agent's context). Morpheus is the nervous system (state persistence + gate enforcement). Now with **task size tiers** (small/medium/large), **greenfield mode**, **batch advance**, and **progress logging**. Agents must submit evidence to advance — the server rejects empty claims.

Together, these form a complete verification infrastructure for AI-assisted development — from project understanding through implementation through quality gate through knowledge persistence. The protocol is not advisory. It is enforced.

---

## Part I: The Problem

### The Five Blindnesses

AI coding agents face five structural gaps that no model improvement will fix:

**1. Project History** — AI can read files. It cannot read institutional memory. Why was that workaround added? Which files always break together? What conventions are actually followed? What approaches were tried and reverted? This intelligence lives in thousands of commits, PR descriptions, and co-change patterns — outside any context window.

**2. Runtime Behavior** — AI can reason about what code should do. It cannot observe what it actually does. CPU spikes, memory leaks, error rates, log anomalies — these require process observation, not code reading. "Happened to look" is not a strategy.

**3. Cross-Service Dependencies** — Change an API field in one service and you silently break downstream consumers. No local test catches it. No linter flags it. The blast radius extends beyond the AI's field of vision.

**4. Code Quality Beyond "Tests Pass"** — AI-generated tests can achieve 100% line coverage while scoring only 4% on mutation testing. They assert that code runs without errors, not that it produces correct results. "All tests pass" is a necessary condition, not a sufficient one.

**5. Web Autonomy** — AI agents cannot act on the web. They can fetch URLs, but they cannot navigate, authenticate, interact with pages, or comprehend what they're looking at. Raw HTML wastes ~93% of tokens on noise. And even when agents get clean content, they lack the hands to click, fill forms, solve Cloudflare challenges, or maintain browser sessions across multi-step workflows. The web is the largest information surface in the world, and AI agents are locked out of it.

### The Numbers

These blindnesses are not theoretical:

- **Veracode (2025)**: 45% of AI-generated code contained CWE vulnerabilities
- **Georgetown CSET**: 68-73% of Copilot/InCoder samples had manually verified vulnerabilities
- **CrowdStrike**: 19% baseline vulnerability rate, jumping to 27.2% with bias triggers
- **GitClear (2025)**: Copy-pasted code rose from 8.3% to 12.3%; refactoring collapsed from 25% to under 10%
- **Qodo (2025)**: Only 3.8% of developers report both low hallucinations AND high confidence shipping AI code
- **Mutation testing research**: AI tests achieve 100% line coverage but only 4% mutation scores

Model improvement addresses reasoning. Blindness requires infrastructure.

---

## Part II: The MCP Suite

### Architecture: Local Sidecar Intelligence

Every tool follows the same pattern:

1. Identify what the AI literally cannot see
2. Build a local sidecar that extracts intelligence from that domain
3. Persist it locally — SQLite, WAL mode, FTS5 search. Single file. No cloud, no Docker
4. Expose it via MCP so the agent can query it like any other tool

The AI doesn't get smarter. It gets informed.

### Shared Engineering DNA

| Layer | Implementation |
|-------|---------------|
| Storage | SQLite + WAL + FTS5. Database at `.tool-name/tool-name.db` |
| Transport | FastMCP over stdio (except Anno: HTTP) |
| Config | `.tool-name/config.toml` → env vars → frozen dataclass defaults |
| CLI | Typer + Rich on stderr (MCP-safe) |
| Output | Markdown, paginated, capped ~4,000 tokens |
| Build | Hatchling, `src/` layout, optional MCP extra |

### The Six Tools

| Blindness | Tool | Version | Tests | MCP Tools | What It Sees |
|-----------|------|---------|-------|-----------|-------------|
| Project history | [**Sentinel**](https://github.com/evo-hydra/sentinel) | 0.4.2 | 329 | 10 | Conventions, pitfalls, decisions, hot files, co-changes, solution memory |
| Runtime behavior | [**Niobe**](https://github.com/evo-hydra/niobe) | 0.2.0 | 14 | 8 | Process metrics, log patterns, error rates, anomalies |
| Cross-service deps | [**Merovingian**](https://github.com/evo-hydra/merovingian) | 0.1.0 | 187 | 10 | API contracts, consumer relationships, breaking changes |
| Code quality | [**Seraph**](https://github.com/evo-hydra/seraph) | 0.1.1 | 187 | 4 | Mutation survival, static analysis, flakiness, risk scoring, security |
| Web autonomy | [**Anno**](https://github.com/evo-hydra/anno) | 2.0.0 | 102 | 12 | Navigate, authenticate, interact, observe, extract, and monitor the web through a stealth browser with persistent sessions |
| Protocol enforcement | [**Morpheus**](https://github.com/evo-hydra/morpheus-mcp) | 0.2.0 | 134 | 6 | Plan state, phase gates, evidence validation, task lifecycle, batch advance, progress logging |

**Total: 6 servers. 50 MCP interfaces. 950+ tests. Open source.**

### Sentinel: Institutional Memory

Extracts from git history: **conventions** (patterns with confidence scores), **pitfalls** (mistakes from reverts/bug fixes with severity), **decisions** (architectural choices with rationale), **hot files** (churn x fragility risk tiers), **co-changes** (file pairs that historically change together), **solution memory** (error fingerprints linked to fixes, searchable via FTS5).

The feedback loop is critical: agents submit accepted/rejected/modified on knowledge entries, recalibrating confidence scores. Over time, Sentinel learns which conventions are actually followed.

### Niobe: Runtime Observation

Snapshot-based, not continuous. Register a service → snapshot (capture metrics via psutil, ingest logs) → make change → snapshot again → compare. Anomaly detection after 3+ baselines flags metrics exceeding mean + 2 sigma.

### Merovingian: Contract Intelligence

Scans OpenAPI specs and Pydantic models. Direction-aware breaking change detection (same change has opposite semantics in request vs response). Consumer registry creates dependency graph. Impact analysis maps blast radius across repos.

### Seraph: Verification Beyond Tests

8-step pipeline: diff → baseline (flaky detection) → mutation testing (mutmut) → static analysis (ruff + mypy) → security scanning (bandit + semgrep + detect-secrets) → Sentinel risk signals → scoring → persistence. Six-dimension grade: mutation score (25%), static cleanliness (20%), test baseline (10%), Sentinel risk (20%), co-change coverage (10%), security (15%, CWE-tier weighted).

### Anno: Web Autonomy for AI Agents

Anno is the only tool in the Nebuchadnezzar suite that acts on the web. Every other tool thinks — Sentinel remembers, Seraph grades, Morpheus enforces. Anno gives AI agents a body on the internet.

**Repositioned in v2.0.0**: Anno was originally framed as a content extractor ("clean text from web noise"). That undersold it. Token reduction is a feature. What Anno actually does is give AI agents the ability to navigate, authenticate, interact with, observe, and extract from the web — through a stealth browser with persistent sessions. The landscape of AI browser tools (Browser Use, Stagehand, Playwright MCP, Firecrawl, Browserbase) splits into three lanes: browser automation ("act on the web"), browser infrastructure ("rent a browser"), and content extraction ("fetch and clean"). Anno occupies a fourth lane that nobody else fills: **web comprehension** — the intelligence layer that turns raw DOM chaos into structured, confidence-scored, provenance-tracked, token-optimized content that an AI can trust.

**12 MCP tools** (up from 5 in v1.0.1):

| Tool | What It Does |
|------|-------------|
| `anno_fetch` | Navigate + extract structured content with 80%+ token reduction |
| `anno_batch_fetch` | Parallel multi-URL extraction (up to 10) |
| `anno_crawl` | Site-wide discovery + extraction with robots.txt compliance |
| `anno_session_auth` | Cloudflare challenge solving + authenticated cookie extraction |
| `anno_interact` | Click, fill, scroll, type — act on web pages through a stealth browser |
| `anno_screenshot` | Visual page capture (MCP image content type) for AI reasoning |
| `anno_page_state` | Interactive element inventory — "what can I click/fill here?" |
| `anno_observe` | Page comprehension — "what am I looking at?" (page type, patterns, navigation) |
| `anno_workflow` | Multi-step browser automation with conditionals, loops, variables |
| `anno_watch` | URL change monitoring with configurable thresholds |
| `anno_search` | Semantic search over previously extracted content |
| `anno_health` | Server health and browser availability check |

**Persistent session threading**: All interaction tools accept `sessionId`/`createSession` parameters. An agent can authenticate once, then navigate, interact, screenshot, and extract across multiple tool calls on the same browser context:

```
session_auth(createSession: true) → interact(sessionId) → screenshot(sessionId) → fetch
```

**Page comprehension (`anno_observe`)**: The tool nobody else has. Returns structured observation of any page: page type classification (login, search-results, article, product, checkout, form, dashboard), interactive element summary, navigation options, and detected patterns (captcha, paywall, cookie consent, auth walls, popups). Uses signal-based weighted classification — extensible without hardcoding rules.

**Content intelligence**: Five extractors in parallel (Readability, DOM heuristic, Trafilatura, domain-specific, Ollama LLM). Confidence scoring selects best result. SHA-256 provenance tracking. Content-addressed caching. Domain policy engine (per-domain YAML). Average 92.7% token reduction — 14 web pages in the space raw HTML uses for one.

**Authentication layer**: `anno_session_auth` navigates to any URL with a real Playwright browser (stealth mode, anti-detection fingerprinting), injects seed cookies, solves Cloudflare challenges, and returns the full cookie jar including `cf_clearance`. Three backing services:

- **AuthManager** — encrypted credential profiles (AES-256-GCM), auto-login workflows with ordered steps
- **SessionManager** — UUID-based browser sessions with TTL, LRU eviction, encrypted cookie persistence to disk
- **PersistentSessionManager** — long-running sessions with natural warming, CAPTCHA detection, and session rotation

Domain-specific extractors (eBay sold listings, Amazon products, Walmart pricing) leverage these sessions for authenticated data access. All endpoints degrade gracefully when Playwright is unavailable.

### Morpheus: Protocol Enforcement

The sixth blindness isn't about what agents can't see — it's about what they skip. Agents rubber-stamp their own quality checks. They assert "FDMC: clean" without reading a sibling file. They claim they checked conventions without running a grep. The protocol is advisory. The agent is biased.

Morpheus fixes this by making the protocol enforceable. It tracks plan state (plans, tasks, phases) in SQLite and requires evidence at each gate:

| Phase | Required Evidence |
|-------|-------------------|
| CHECK | *(none — entry point)* |
| CODE | `sibling_read` — file path of the sibling read for the Consistent check (or "N/A" for greenfield) |
| TEST | Build verification output |
| GRADE | Test results + `fdmc_review` — post-code FDMC lens one-liner |
| COMMIT | Seraph assessment ID (or `skip_reason` for intentional skips) |
| ADVANCE | Knowledge gate: solution saved, solution verified, `"nothing_surprised"` + reason, or `"true"` |

**Adaptive protocol (v0.2.0)**: Morpheus now adapts gate strictness based on task complexity and project maturity:

- **Task size tiers** (`size: small/medium/large` in plan files) — SMALL tasks skip fdmc, seraph, and knowledge gates for lightweight pass-through. LARGE tasks enforce Seraph grading even when `grade: false`. MEDIUM tasks (default) get the full protocol.
- **Greenfield mode** (`mode: greenfield` in plan frontmatter) — relaxes `sibling_read` requirement when all files are new and there are no existing siblings to read.
- **Batch advance** (`morpheus_advance_batch`) — processes multiple phase advances in a single MCP call, cutting 80%+ of protocol overhead for small/tail-end tasks.
- **Progress logging** (`morpheus_progress`) — log observational progress without advancing phases, visible in `morpheus_status` output.
- **Merged FDMC** — FDMC is now a single post-code pass (Phase 4a), not a pre-flight + post-review dual pass. CODE phase requires only `sibling_read` (the check that actually prevents bugs). GRADE phase includes the full FDMC review. This halves FDMC ceremony without losing enforcement value.

Three layers of enforcement work together. **Evidence validation**: agents cannot advance past CODE without proving they read a sibling file — the server rejects empty or missing evidence. **Sequential ordering**: the server enforces phase order — an agent cannot skip from CHECK to ADVANCE without completing every intermediate phase. **Size-aware gates**: the server adjusts strictness per task, preventing the 6-phase ceremony from being applied uniformly to 30-line components and 200-line subsystems alike. These gates together prevent both the #1 FDMC violation (Consistent lens failures) and the broader pattern of agents optimizing for speed by skipping "slow" verification steps.

The Dev Loop skill (`/morpheus`) remains the brain — it loads the full protocol into the agent's context. Morpheus MCP is the nervous system — it persists state and rejects invalid advances. The skill gives agents understanding. The server gives them guardrails.

**Why MCP matters here**: Because Morpheus is an MCP server — not a Claude Code plugin, not a prompt, not a framework-specific hook — any agent from any framework can connect and get governed by the same gates. A Claude Code session, a CI pipeline bot, an Agent SDK deployment, a competitor's framework — all connect to the same Morpheus server, all follow the same protocol, all submit the same evidence. The quality standard is portable because it's infrastructure, not instructions.

---

## Part III: FDMC — The Quality Standard

### The Four Lenses

FDMC encodes the judgment that models lack. Applied as a single post-code review pass (v3.2+ merged the pre-flight and post-review into one):

- **Future-Proof** — Will this break when requirements change? Avoid tight coupling to current assumptions. Am I baking in assumptions about callers, data shapes, or execution order?

- **Dynamic** — Am I hardcoding something that should be configurable? Prefer parameters over literals. Magic numbers, paths, thresholds, limits — should these be config?

- **Modular** — Does this have one clear responsibility? If you can't describe it in one sentence, split it. If you're touching 5+ files, you're doing too many things.

- **Consistent** — Does this follow existing patterns in the codebase? Match what's already there before inventing something new. **This is the most commonly violated lens.**

### FDMC Anti-Patterns

- God objects or classes doing too many things
- Hardcoded secrets or environment-specific values
- Catching broad exceptions silently
- Copy-pasting instead of extracting shared logic
- Adding abstractions for things that only happen once
- Writing code for hypothetical future requirements
- Creating parallel types when equivalents exist
- Standalone classes when siblings are manager-owned
- Different integration patterns than existing code

### FDMC as Enforced Gate (Not Just a Checkpoint)

FDMC v1 was "read these bullet points before coding." It was ignored.

FDMC v2 added two checkpoints (pre-code and post-code) with concrete red flags. It was an improvement, but the agent still rubber-stamped its own checks. During the hex-engine case study, the agent wrote `FDMC: Consistent — matched existing pattern` without ever reading a sibling file. The sunk-cost bias is real: the agent just wrote the code, so of course it thinks the code is correct.

FDMC v3 made the checkpoint an **enforced gate** via Morpheus MCP. v3.2 streamlines it further: **one FDMC pass, post-code, with the consistency check as the only pre-code gate.**

**Pre-code (Phase 2a)**: Read one sibling file. Submit the file path as `sibling_read` evidence to Morpheus. The other three lenses (Future-Proof, Dynamic, Modular) are a mental checklist — internalized, not gated. This focuses enforcement on the check that actually prevents bugs (Consistent) while removing ceremony from the checks that are self-evident during coding.

**Post-code (Phase 4a)**: The single FDMC review pass. Self-review the diff through all four lenses. Submit the `fdmc_review` one-liner as part of GRADE evidence. Concrete red flags:

| Lens | Red Flag |
|------|----------|
| Future-Proof | Struct fields that duplicate existing types. APIs that assume a specific caller. |
| Dynamic | Magic numbers, embedded strings, hardcoded paths. |
| Modular | Function doing parsing AND validation AND storage. |
| Consistent | New standalone class when siblings are manager-owned. New data type when equivalent exists. Different integration pattern. |

Structural violations must be fixed before grading. The FDMC result is recorded in the commit message, creating an audit trail.

---

## Part IV: The Dev Loop Protocol

### The Missing Layer

The MCP tools are sensors. FDMC is the quality standard. But neither tells the agent *when* to look, *what* to look at, or *how* to respond to what it finds. And even when told, the agent skips steps under context pressure.

The Dev Loop is the orchestration protocol that connects them. Morpheus is the enforcement layer that ensures they actually run.

```
Agents (any framework)
    ↓ query
Morpheus MCP (plan state + phase gates + evidence validation)
    ↓ orchestrates
Sentinel / Seraph / Niobe / Merovingian / Anno (intelligence)
    ↓ reads
Code / Git / Runtime / APIs / Web
```

### Protocol Overview

```
BOOTSTRAP (once per plan)
    |
    v
+-- CHECK --> CODE --> TEST --> FDMC CRITIQUE --> GRADE --> COMMIT --> ADVANCE --+
|                                                                                |
+-------------------------------- next task ------------------------------------+
    |
    v
CLOSE (once per plan)
```

### Phase 0: BOOTSTRAP (once)

Probe all MCP servers. Cache availability flags. Don't re-probe per task.

```
sentinel_project_context  --> SENTINEL_AVAILABLE = true/false
seraph_history            --> SERAPH_AVAILABLE = true/false
niobe (if services exist) --> register test runner / server
merovingian (if APIs)     --> register + scan repo
```

This single call to `sentinel_project_context` replaces what v1 did with 4 separate calls per task, all of which failed silently and were never retried intelligently.

### Phase 1: CHECK (per task)

If Sentinel available:
- `sentinel_pitfalls(file_path)` — what went wrong here before?
- `sentinel_co_changes(file_path)` — what else needs to change?
- For high-risk tasks: `sentinel_decisions`, `sentinel_query(keywords)`

If Merovingian available and task modifies APIs/serialized types:
- `merovingian_breaking` + `merovingian_impact`

### Phase 2: CODE (consistency check + implementation)

Read one sibling file. Submit `sibling_read` evidence to Morpheus. Mentally check Future-Proof, Dynamic, Modular. Then implement minimum code to satisfy acceptance criteria.

### Phase 3: TEST

Run test command. If Niobe registered: snapshot before/after, compare for regressions. On failure: `sentinel_solution_search` before debugging from scratch. Max 3 retries.

### Phase 4: FDMC CRITIQUE + GRADE

**4a.** Single FDMC pass: self-review diff through all four lenses. Fix structural violations (parallel types, wrong ownership model). Submit `fdmc_review` one-liner as part of GRADE evidence.

**4b.** `seraph_assess` with `ref_before=<previous commit>` to grade only this task. Do NOT skip mutations. A/B: proceed. C: fix. D/F: fail task.

### Phase 5: COMMIT + KNOWLEDGE

Git commit with FDMC note. If Sentinel available: `sentinel_solution_save` for any bugs fixed, `sentinel_solution_verify` on solutions used, `[PITFALL]` prefix for non-obvious gotchas.

### Phase 6: ADVANCE

Update plan status. Print progress. Next task.

### Phase 7: CLOSE (once)

Feedback sweep: `sentinel_feedback`, `seraph_feedback`, `niobe_feedback` on all entries used. This closes the learning loop — every server gets smarter for the next session. Optionally create a PR.

### Why This Matters

The Dev Loop is the first autonomous development protocol that integrates project intelligence (Sentinel), quality verification (Seraph), runtime observation (Niobe), and dependency analysis (Merovingian) into a single coherent cycle with FDMC self-critique and feedback loop closure.

No other tool or framework combines all of these capabilities. Each competitor excels in specific areas:

| Capability | Aider | Cline | Kiro | Open SWE | Ruflo | Dev Loop |
|-----------|-------|-------|------|----------|-------|----------|
| Lint-test-fix loop | **Yes** | **Yes** | Hooks | No | No | Yes |
| Plan-first workflow | No | **Yes** | **Yes (specs)** | **Yes** | **Yes** | Yes |
| Separate reviewer | No | No | No | **Yes** | **Yes (swarm)** | No (Gap A) |
| Audit trail | Commits | **Full** | No | Logs | Logs | Commits |
| Pre-flight intelligence | No | No | No | No | No | **Yes (Sentinel)** |
| Mutation testing gate | No | No | No | No | No | **Yes (Seraph)** |
| Runtime observation | No | No | No | No | No | **Yes (Niobe)** |
| Contract verification | No | No | No | No | No | **Yes (Merovingian)** |
| Security scanning | No | No | No | No | No | **Yes (Seraph)** |
| Enforced quality gates | No | No | No | No | No | **Yes (Morpheus)** |
| Feedback loop closure | No | No | No | No | No | **Yes (*_feedback)** |
| Knowledge persistence | No | Logs | No | No | **Yes (HNSW)** | **Yes (solution_save)** |
| Multi-agent parallelism | No | No | No | **Yes** | **Yes (swarms)** | No (Gap A) |
| Model routing / cost opt | No | No | No | No | **Yes** | No |

---

## Part V: Empirical Evidence

### Case Study: hex-engine (March 14, 2026)

The Dev Loop v1 was first run against hex-engine, a C++ strategy game engine with 25+ test suites, 70+ source files, and Raylib graphical client.

**Plan**: 12 tasks from a launch plan document. Data loading, spectator UI, narrator, safety metrics, anomaly detection, camera AI, trace export, arena modes.

**Results**: 12/12 tasks completed. 11 commits. 415 tests passing. ~4,800 lines of code added across 20+ files.

### What Went Wrong (v1 Failures)

| Failure | Impact |
|---------|--------|
| Called 4 Sentinel tools per task; all returned "not initialized" | 48 wasted MCP calls, zero intelligence gathered |
| Called `seraph_assess` once with `skip_mutations=true` | Grade was 3/6 dimensions, A by default — meaningless |
| Never called `sentinel_project_context` (the single most important call) | Missed the one-shot context load |
| Never called `sentinel_solution_save` after fixing a compile error | Lost knowledge that should have been persisted |
| Never submitted feedback to any server | Broke the learning loop |
| Never used Niobe or Merovingian | Missed runtime and contract intelligence |
| Used 4 of 32 available MCP tools | 87.5% of available intelligence unused |

### FDMC Violations Detected in Self-Critique

| Task | Violation | Lens |
|------|-----------|------|
| Task 1 | Created `hex::ArtifactData` when `hex::game::ArtifactData` already existed in progression.h | **Consistent** |
| Task 2 | Had to write conversion functions between parallel types | Consequence of Task 1 violation |
| Task 7 | `SafetyMetricsTracker` is standalone — every other subsystem is owned by `GameManager` | **Consistent** |
| Task 10 | `AnomalyDetector` is standalone — same pattern violation | **Consistent** |
| Task 3 | All UI functions inline in a header — will bloat compile times | **Modular** (minor) |

### Case Study: morpheus-mcp (March 15, 2026)

The Dev Loop v2 built its own orchestrator. morpheus-mcp is the enforcement layer for the protocol, and it was built entirely by the protocol it enforces — the dev loop bootstrapping its own enforcement infrastructure.

**Plan**: 10 tasks. Project scaffold, models, parser, SQLite store, gate engine, tests, MCP server (4 tools), CLI, integration tests, packaging.

**Results**: 10/10 tasks completed. 11 commits. 81 tests passing. 88% coverage. Published to PyPI as `morpheus-mcp 0.1.0`.

**What the protocol caught during its own construction:**
- Task 9: Tests caught a formatter bug — plan IDs were truncated to 12 characters, making `morpheus_close` unusable via MCP. The test forced the fix before it shipped.
- Task 10: Build system rejected an absolute symlink (`.plan_file`). Added to `.gitignore`.
- Post-build: The author (Claude) created `~/.pypirc` for PyPI auth. The user caught a Consistent violation — all sibling projects use `.env` files in the project root, not `~/.pypirc`. This is exactly the kind of pattern mismatch that the `sibling_read` gate is designed to prevent.

**Key insight**: The agent that designed the FDMC enforcement system violated FDMC while building it. This validates the entire thesis — self-critique is insufficient. Enforcement is necessary.

### Case Study: morpheus-mcp v0.2.0 Adaptive Protocol (March 19, 2026)

The Dev Loop v3 was used to enhance itself. A 25-task greenfield run produced detailed feedback on protocol pain points, which was analyzed and turned into a 20-task plan to make the protocol adaptive. The plan was then executed by the protocol it was improving — the second bootstrap.

**Plan**: 20 tasks. Task size tiers (models, parser, store, engine, tests), greenfield mode (model, store, engine, tests), batch advance (engine, server, tests), progress logging (store, server, formatters, tests), FDMC merge (skill, engine, tests), formatter polish, integration test.

**Results**: 20/20 tasks completed. 16 commits. 121 tests passing (up from 83). Zero regressions. Test suite grew 46% while maintaining zero failures throughout all 20 task cycles.

**What the protocol proved about itself:**
- The 6-phase ceremony worked well for tasks 1-10 (real implementation). By tasks 14-20 (tests and polish), the overhead was noticeable — exactly the feedback the plan was designed to fix.
- Seraph grading was vacuous because morpheus-mcp lives as a subdirectory of a non-git-root monorepo. This confirmed the "greenfield mode" need — tools that assume git context need graceful degradation.
- Backward compatibility for `fdmc_preflight` format was needed — the running Morpheus MCP server still had old gates while we were shipping the new ones. The old format auto-extracts `sibling_read` from the nested JSON.
- The protocol dogfooding its own improvement is the strongest possible validation: if the protocol can't improve itself efficiently, it can't improve anything else.

### Protocol Evolution

Each case study drove targeted protocol changes. The pattern was consistent: dogfood → discover what agents skip or game → add enforcement or remove dead weight.

**v1 → v2** (informed by hex-engine): Added bootstrap-once probing, single-call context loading (`sentinel_project_context`), per-commit Seraph grading with mutations enabled, feedback sweep at plan close. Core principle: *"Every MCP call should influence your next action — don't call tools ritualistically."*

**v2 → v3** (informed by morpheus-mcp build): Replaced markdown state tracking with SQLite-backed Morpheus MCP. Added evidence gates — agents must submit a `sibling_read` file path to advance past CODE, and a knowledge artifact or reason to advance past COMMIT. Architecture split: skill = brain (protocol understanding), MCP = nervous system (state + enforcement).

**v3 → v3.2** (informed by 25-task greenfield run): ~40% of the protocol was dead weight for small tasks. Added task size tiers (small skips ceremony, large enforces it), greenfield mode, batch advance (80%+ overhead reduction), and merged FDMC into a single post-code pass. Dogfooding on Zado then revealed gates enforcing *form* over *substance* — 8/10 tasks rubber-stamped with bare `"nothing_surprised"`. Fix: gates now require articulated reasons, not just strings.

**Key insight**: Gates that check for *form* (did you submit a string?) train agents to game the system. Gates that check for *substance* (did you articulate why?) train agents to think. Same lesson as code coverage metrics — the metric is only useful if gaming it is harder than doing the work.

### v3.2 → v3.3 Changes

Anno's repositioning from content extractor to web autonomy layer, executed via the Morpheus dev loop (10/10 tasks, 8 commits):

- **Anno v2.0.0**: 7 new MCP tools expose capabilities that were previously hidden behind REST-only routes. AI agents can now interact with pages (`anno_interact`), see pages (`anno_screenshot`), discover interactive elements (`anno_page_state`), comprehend pages (`anno_observe`), execute multi-step workflows (`anno_workflow`), monitor URLs (`anno_watch`), and search extracted content (`anno_search`).
- **Persistent session threading**: All interaction tools accept `sessionId`/`createSession` for multi-step authenticated browsing across tool calls. `session_auth → interact → screenshot → fetch` on a single browser context.
- **Page comprehension (`anno_observe`)**: New page-observer service classifies page types (login, search-results, article, product, checkout, etc.), inventories interactive elements, detects patterns (captcha, paywall, cookie consent), and summarizes content — in one call.
- **Identity shift**: Reframed the 5th Blindness from "Web Content" to "Web Autonomy." Token reduction is a feature, not the identity. The identity is: Anno gives AI agents a body on the internet.

**Key insight**: Anno had the capabilities — stealth browser, encrypted sessions, Cloudflare solving, domain-specific extraction, interaction routes, workflow engine, URL monitoring. But only 5 of 12 possible tools were exposed through MCP. AI agents literally could not see 80% of what Anno could do. The fix was not building new things — it was surfacing what was already built. The gap between what a tool can do and what an agent can see it do is itself a form of blindness.

### Why Enforcement Matters

During the development of Morpheus MCP, the AI agent (Claude Opus) was asked directly: "Why did you skip FDMC? Is it not useful?"

> *"I rubber-stamped it. The skill says 'read one sibling.' What I actually did was write 'FDMC: Consistent — matched existing pattern' without reading a sibling file. I asserted the check passed without doing the check."*

The reasons: **no enforcement** (the skill is a prompt, not a gate), **sunk-cost bias** (the agent just wrote the code — of course it thinks it's fine), **context pressure** (under a 12-task plan, the agent optimizes for throughput and skips the "slow" checks that happen to be the most valuable), and **it feels like overhead** (the code compiles, tests pass — why read a sibling file?).

The proof came during Morpheus's own construction: the agent created `~/.pypirc` for PyPI authentication while every sibling project uses `.env` files in the project root. The agent that designed the `sibling_read` gate violated the Consistent lens while building the gate.

You can't fix a behavioral problem with a better prompt. You fix it with a gate that won't open until the agent shows its work. Same reason CI pipelines exist instead of asking developers to "please run the tests before you push." Understanding without enforcement is ignored. Enforcement without understanding is robotic. The combination produces agents that think *and* prove they thought.

---

## Part VI: Competitive Landscape

### The Spectrum

```
No structure -----> IDE Assistants -----> Agent Frameworks -----> Dev Loop v3
                   (rules + lint)        (ReAct loops)           (MCP intelligence +
                                                                  FDMC critique +
                                                                  mutation grading +
                                                                  enforced gates +
                                                                  feedback closure)
```

### Key Players

**Aider** — Closest to a real dev loop. Auto-lint, auto-test, auto-commit after every edit. No pre-flight intelligence, no mutation testing, no self-critique.

**Cline** — Plan-Act-Verify with full audit trail. Human approval required per action. No MCP intelligence, no mutation testing.

**Kiro (AWS)** — Spec-driven development: user stories → technical design → tasks. Most structured pre-flight. No runtime observation, no feedback loops.

**Open SWE (LangChain)** — Multi-agent with Planner + Reviewer. Closest to FDMC critique via separate reviewer. No project intelligence.

**Ruflo (ruvnet)** — Claude-native multi-agent orchestration. 259 MCP tools, 60+ agent types, swarm topologies (mesh/hierarchical/ring/star), multi-model routing, HNSW vector memory. Broadest orchestration surface. No project intelligence, no mutation testing, no enforced evidence gates. Optimizes for parallelism and cost; EvoIntel optimizes for verification and intelligence. Complementary philosophies — Ruflo asks "how many agents can work at once?" while EvoIntel asks "how do we know the work is correct?"

**Anthropic's own guidance** — Writer/Reviewer pattern (two separate sessions). Recommends hooks for deterministic quality gates. Emphasizes "give Claude a way to verify its own work."

### What We Do That Nobody Else Does

1. **MCP-powered project intelligence as pre-flight** — Zero public MCP servers provide convention/pitfall/co-change intelligence to coding agents
2. **Mutation testing as quality gate** — No other autonomous agent protocol runs mutations
3. **FDMC self-critique with concrete red flags** — More structured than any single-agent self-review
4. **Feedback loop closure** — Agents learn across sessions via `*_feedback` tools
5. **Knowledge persistence** — Error fingerprints linked to fixes, searchable across sessions
6. **Web comprehension layer** — Anno is the only MCP tool that combines page classification, interactive element discovery, pattern detection, confidence-scored extraction, and persistent browser sessions into a single coherent surface. Browser Use gives agents hands. Anno gives agents understanding.

### What Others Do Better

1. **Separate reviewer agent** (Anthropic, Open SWE) — Self-critique has inherent bias; a fresh context catches more
2. **Spec-first** (Kiro) — We skip the spec layer; complex features benefit from data model + API surface design before task decomposition
3. **Tighter edit-lint-test** (Aider) — Lint and test after every edit, not just at end of task
4. **Granular audit trail** (Cline) — We log at plan/commit level, not individual tool calls

### Industry Trends

- **MCP is now an industry standard** — Donated to the Agentic AI Foundation (Linux Foundation), co-governed by Anthropic, OpenAI, and Block. 97M+ monthly SDK downloads.
- **AGENTS.md** — Open spec for AI agent instructions, adopted by 20,000+ GitHub repos.
- **From "vibe coding" to agentic engineering** — Coined by Karpathy (Feb 2025), the term described prompt-driven development without structure. Industry is shifting toward structured agent workflows with verification.
- **Multi-agent is the direction** — Writer/Reviewer, Planner/Executor, Architect/Programmer patterns emerging everywhere.
- **Commit frequency 4x** — Gene Kim's "Three Developer Loops" recommends committing every few minutes with agents.

---

## Part VII: Anthropic Alignment

### Independent Convergence

EvoIntel was built without knowledge of Anthropic's internal research on agentic coding challenges. In March 2026, a systematic review of Anthropic's published research revealed significant overlap — every major problem they identified has a corresponding EvoIntel solution, arrived at independently from the same observations about what breaks in practice.

### 2026 Agentic Coding Trends Report

Anthropic's flagship report on what's broken in agentic coding. The alignment:

| Anthropic Identifies | EvoIntel Addresses |
|---------------------|-------------------|
| **Delegation gap** — 60% usage but only 0-20% full delegation. Developers don't trust agents autonomously. | **Seraph + FDMC + Morpheus** — Mutation testing quality gates + enforced evidence gates. Trust through verification, not faith. |
| **Quality at scale** — AI-automated review systems are "not optional, essential" for managing agent output. | **Seraph** — Automated mutation testing grade per commit. **Morpheus** — Gate enforcement across plans. |
| **Oversight without bottleneck** — Need meaningful oversight, not rubber-stamping every action. | **Dev Loop phases** — Structured checkpoints where intelligence is gathered and quality is verified, without requiring human approval per action. |
| **Security as architecture** — Same capabilities that accelerate dev can be misused. | **Seraph** — Security as a 6th grading dimension: Bandit + Semgrep + detect-secrets with CWE-tier weighting. Tier 1 vulnerabilities (injection, XSS, hardcoded credentials) weighted 3x. |
| **Multi-agent coordination** — Agents duplicate work, leave gaps, misinterpret tasks. | **Morpheus MCP** — Framework-agnostic protocol enforcement. Any agent connects via MCP and gets governed by the same gates. |

### Effective Context Engineering for AI Agents

Anthropic's technical guide reads like a description of the Five Blindnesses:

| Anthropic Identifies | EvoIntel Addresses |
|---------------------|-------------------|
| **Context rot** — Model accuracy degrades as context grows (attention budget). | **MCP sidecars** — Pre-computed intelligence served on demand, not loaded into context. ~4,000 token caps per response. |
| **Tool bloat** — "Bloated tool sets that cover too much functionality" undermine agents. | **Design constraint #1** — "No monolithic 20+ tool servers." Six focused tools, each with one responsibility. |
| **Long-horizon coherence** — Agents can't maintain state across sessions. | **Sentinel** — Solution memory persists across sessions via FTS5. **Morpheus** — Plan state persists in SQLite. |
| **Context as finite resource** — Agents need pre-computed intelligence, not runtime exploration. | **Every MCP sidecar** — Sentinel pre-computes conventions from git history. Merovingian pre-scans API contracts. Niobe pre-captures runtime baselines. |

### Other Anthropic Research

- **Property-Based Testing (NeurIPS 2025)** — Anthropic acknowledges conventional testing can't "exhaustively cover a program." This is the exact problem Seraph's mutation testing addresses.
- **Measuring AI Agent Autonomy** — Identifies the need for "new forms of post-deployment monitoring infrastructure." That is Niobe.
- **Building Effective Agents** — "We spent more time optimizing our tools than the overall prompt." This is the EvoIntel thesis in one sentence.

### The Implication

Anthropic built the models. EvoIntel built the infrastructure those models need to work reliably. The Five Blindnesses are not a criticism of Claude — they are structural limitations of any LLM operating without external intelligence. The solution is not better models. The solution is better sensors, better enforcement, and better feedback loops.

---

## Part VIII: Remaining Gaps

### Gap A: Single-Agent Self-Review

FDMC critique is the agent reviewing its own work. It has inherent sunk-cost bias. The strongest pattern in the industry is a separate reviewer with fresh context.

**Resolution**: Add a `/review` subagent to the dev loop that runs after FDMC critique with a clean context window. Or run FDMC critique via a subagent with `isolation: "worktree"`.

### Gap B: No Unified Verdict Surface (Partially Resolved)

Each sidecar is strong in isolation. No single endpoint returns a combined pre-merge risk verdict across quality, contracts, history, and runtime.

**Partial resolution**: Morpheus absorbs the orchestration role originally planned for Oracle. The COMMIT gate in Morpheus requires a Seraph assessment ID, and the CHECK phase queries Sentinel and Merovingian. This creates a de facto verdict surface — the agent cannot advance without satisfying gates that span multiple sidecars. A dedicated `morpheus_verdict` tool that aggregates all sidecar signals into a single go/no-go decision is the natural next step.

### ~~Gap C~~ (Resolved): Security Scoring in Seraph

Seraph now includes security as a 6th grading dimension (15% weight). Three engines run in parallel: Bandit (50+ test IDs mapped to 25+ CWE IDs), Semgrep (configurable rules with CWE metadata extraction), and detect-secrets (12+ secret types). CWE-tier weighting: Tier 1 (injection, XSS, command injection) = 3x, Tier 2 (crypto, SSRF) = 2x. False-positive filters for CWE-259 and CWE-330.

### Gap D: Merovingian Phase-2 Intelligence

Contract drift detection (spec vs observed runtime) and cross-repo co-change prediction are not yet implemented.

### ~~Gap E~~ (Largely Resolved): Agent Web Autonomy

AI agents could not authenticate to or interact with web platforms. Anno v2.0.0 resolves the browser surface comprehensively: `anno_session_auth` solves Cloudflare challenges, `anno_interact` clicks/fills/scrolls, `anno_observe` classifies pages and detects patterns, `anno_screenshot` provides visual context, and persistent `sessionId` threading allows multi-step authenticated workflows across tool calls. AuthManager stores encrypted credential profiles with auto-login workflows. Agents can now navigate to a login page, observe it, fill credentials, submit, verify success, and extract content — all through MCP tools on a single persistent browser session.

**What remains**: Scoped API tokens (not browser cookies), auditable access logs for agent identity, and a credential vault with role-based access control. The current implementation is single-user — it uses the operator's browser session, not a scoped agent identity. A full solution would separate agent credentials from operator credentials and provide audit trails for which agent accessed which platform with what permissions.

### Gap F: Dev Loop Spec-First Layer

Complex features would benefit from a spec document (data model, API surface, integration points) before task decomposition. Currently the plan goes directly from description to tasks.

### Gap G: Adversarial Resilience and Memory Provenance

EvoIntel was designed for quality verification under cooperative conditions — the agent is trying to do good work, and the suite helps it do better work. It was not designed for adversarial conditions — where a nefarious actor actively tries to compromise the agent through prompt injection, tool poisoning, or memory manipulation.

This matters because Sentinel's persistent memory (`solution_save` / `solution_search`) is simultaneously the compound learning mechanism AND a potential poisoning surface. If an agent is manipulated (via indirect prompt injection through web content, malicious MCP tool descriptions, or crafted repository content) into saving adversarial "knowledge" to Sentinel, that poison persists across sessions and compounds — exactly as designed, but serving the attacker. The same property that makes EvoIntel valuable for quality makes it vulnerable to adversarial exploitation.

Current industry guidance (OWASP AI Agent Security, OpenAI agent safety, Anthropic Claude Code security) converges on boundary enforcement: least privilege, source-sink analysis, approval gates, memory sanitization, and sandboxing. EvoIntel's architecture (local-first MCP sidecars, evidence gates, compound verification) is structurally compatible with these controls but does not yet implement them.

**What would be needed:** (1) Provenance and trust-level tagging on all Sentinel entries — source, timestamp, trust tier, quarantine flag. (2) Memory sanitization — scan entries for instruction-like patterns before persisting. (3) Pre-execution tool boundary enforcement — a policy layer that validates tool calls before they execute, not after. (4) Taint tracking on content from untrusted sources (web, email, external MCP servers). (5) Behavioral anomaly detection at the agent action level, not just periodic runtime snapshots.

This gap is noted here as an honest architectural limitation. EvoIntel provides sensors, memory, and verification. An adversarial safety layer would require a policy/control plane on top of these primitives — a distinct engineering effort that builds on the existing architecture rather than replacing it.

---

## Part IX: Roadmap

### Done (Q1 2026) ✓

1. ~~Build Morpheus MCP~~ — plan state + phase gates + evidence validation. Published to PyPI as `morpheus-mcp`. 83 tests.
2. ~~FDMC enforcement gates~~ — `sibling_read` requirement, knowledge gate, Seraph ID gate.
3. ~~Anthropic alignment analysis~~ — mapped EvoIntel against 2026 Agentic Coding Trends Report.
4. ~~Wire `/morpheus` skill to Morpheus MCP~~ — skill now calls `morpheus_init`, `morpheus_advance` (all 6 gates), and `morpheus_close`. No more markdown-only state.
5. ~~Sequential phase ordering enforcement~~ — `_PHASE_ORDER` wired into `advance()`. Server rejects out-of-order advances. Phases cannot be skipped.
6. ~~Seraph security dimension~~ — Bandit + Semgrep + detect-secrets as 6th grading dimension (15% weight, CWE-tier weighted). 187 tests.
7. ~~Morpheus adaptive protocol~~ — Task size tiers (small/medium/large), greenfield mode, batch advance, progress logging, merged FDMC single-pass, simplified knowledge gate. 134 tests. 16 commits. 20 tasks completed autonomously via the protocol itself.
8. ~~Morpheus gate quality~~ — Rejection format examples, knowledge_reason requirement, skip_reason parameter. Dogfooded on Zado (zadofi.ai). 7 tasks, 6 commits.
9. ~~Anno web autonomy (v2.0.0)~~ — Repositioned from content extractor to web autonomy layer. 7 new MCP tools (interact, screenshot, page-state, observe, workflow, watch, search). Persistent sessionId threading across all interaction tools. New page-observer service for page comprehension. Rebranded all tool descriptions. 12 MCP tools total. 2,868 tests. 8 commits. Executed via Morpheus dev loop (10/10 tasks).

### Now: Validation + Polish (Q1-Q2 2026)

1. ~~Test Morpheus enforcement on real projects~~ — ran 25-task greenfield + 20-task self-improvement via the protocol. Dogfooding feedback drove v0.2.0 adaptive protocol.
2. Initialize Sentinel on all active projects (`sentinel init`)
3. Enable full Seraph grading (with mutations) and calibrate grade thresholds
4. Add `/review` subagent for separate-context FDMC critique
5. Publish dev-loop plugin to Claude Code plugin marketplace

### Next: Verdict + Enterprise Tooling (Q2 2026)

1. Add `morpheus_verdict` tool — aggregates Sentinel + Seraph + Merovingian + Niobe into a single go/no-go decision
2. Add Snyk Studio MCP for enterprise-grade SCA/SBOM
3. Publish the Five Blindnesses article + launch content sequence
4. Spec-first layer in `/plan` command for complex features

### Later: Calibration + Cross-Sidecar Wiring (Q3-Q4 2026)

1. Closed-loop calibration: map predicted risk (Seraph grade) to observed outcomes (Niobe runtime signals post-deploy)
2. Weight tuning: adjust verdict scoring by empirical false positives/negatives
3. Policy gates: block merge if critical security findings > 0, if breaking consumer impact unresolved, if runtime regression above threshold
4. Compliance exports: PCI DSS 6.2.3, SOC 2, SSDF evidence formatting
5. Signed audit trail across all tools
6. Niobe → Sentinel auto-pitfalls: runtime anomalies Niobe detects become Sentinel pitfall entries automatically, so the next dev loop session sees them in CHECK phase without manual `sentinel_solution_save`
7. Merovingian → Morpheus gate: breaking changes detected by Merovingian block ADVANCE until consumer impact is resolved — the data exists today but the enforcement doesn't
8. Seraph → Sentinel convention detection: recurring mutation-surviving patterns across plans become Sentinel conventions automatically, closing the loop between grading and project intelligence

### Horizon: Multi-Agent + Platform (2027)

1. Dedicated reviewer agent with fresh context (replacing self-critique)
2. Agent authentication infrastructure (scoped tokens, encrypted vaults)
3. Contract drift detection (Merovingian + Niobe)
4. Cross-repo co-change prediction (Merovingian + Sentinel)
5. SaaS offering for teams (hosted Oracle + dashboard)

---

## Part X: The Compounding Knowledge Loop

The suite creates value through three habits:

1. **"Catch me up"** at session start — `sentinel_project_context` loads conventions, pitfalls, decisions, and hot files in one call. The agent starts informed, not blind.
2. **"Check before you start"** before coding — `sentinel_pitfalls` + `sentinel_co_changes` surface what went wrong here before and what else needs to change. The agent codes with history, not assumptions.
3. **"Commit and remember"** instead of just "commit" — `sentinel_solution_save` persists what the agent learned (pitfalls, fixes, patterns). Task 7 benefits from what Task 1 discovered.

Each habit feeds the next session. Sentinel's confidence scores adjust based on feedback. Solutions accumulate and get verified. The system gets more useful the more it's used — not because the model improves, but because the infrastructure remembers.

---

## Part XI: Installation

```bash
# The MCP Suite
pipx install git-sentinel       # Project intelligence
pipx install niobe              # Runtime observation
pipx install merovingian        # Dependency intelligence
pipx install seraph-ai          # Verification intelligence
pipx install morpheus-mcp       # Protocol enforcement
npm install -g @evointel/anno   # Web autonomy for AI agents

# The Dev Loop (Claude Code plugin)
# Install from Claude Code plugin marketplace or:
# Copy dev-loop/ to ~/.claude/plugins/
```

Source: [github.com/evo-hydra](https://github.com/evo-hydra) | [evolvingintelligence.ai](https://www.evolvingintelligence.ai)

---

## Appendix A: Design Constraints

1. **No tool bloat** — No monolithic 20+ tool servers. Six focused tools, each with one clear responsibility.
2. **Separable sidecars** — No tight coupling. Morpheus orchestrates but doesn't absorb. Each tool works independently.
3. **Local-first** — SQLite + MCP. No cloud, no Docker, no external databases.
4. **Concise output** — Pagination, truncation, ~4,000 token caps. High signal per token.
5. **Backward compatible** — Anno API/CLI/MCP surfaces are stable.
6. **Matrix naming** — Sentinel, Niobe, Seraph, Merovingian, Morpheus. Anno is the exception.
7. **Brain + nervous system** — Protocol logic lives in the skill prompt (the brain). State and enforcement live in MCP servers (the nervous system). This separation means any agent framework can follow the protocol by connecting to the MCP servers.

## Appendix B: References

### Empirical Research
- [Veracode 2025 GenAI Code Security Report](https://www.veracode.com/resources/analyst-reports/2025-genai-code-security-report/)
- [Georgetown CSET — Cybersecurity Risks of AI-Generated Code](https://cset.georgetown.edu/publication/cybersecurity-risks-of-ai-generated-code/)
- [CrowdStrike — Hidden Vulnerabilities in AI-Coded Software](https://www.crowdstrike.com/en-us/blog/crowdstrike-researchers-identify-hidden-vulnerabilities-ai-coded-software/)
- [GitClear AI Code Quality 2025](https://www.gitclear.com/ai_assistant_code_quality_2025_research)
- [Qodo State of AI Code Quality 2025](https://www.qodo.ai/reports/state-of-ai-code-quality/)
- [Mutation testing vs AI coverage (TwoCents)](https://www.twocents.software/blog/how-to-test-ai-generated-code-the-right-way/)

### Standards
- [OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/resource/owasp-top-10-for-llm-applications-2025/)
- [OpenSSF Security-Focused Guide for AI Code Assistants](https://best.openssf.org/Security-Focused-Guide-for-AI-Code-Assistant-Instructions)
- [NIST SP 800-218A — AI SSDF Community Profile](https://csrc.nist.gov/pubs/sp/800/218/a/final)
- [AGENTS.md specification](https://agents.md/)

### Anthropic Research (Alignment Analysis)
- [2026 Agentic Coding Trends Report](https://resources.anthropic.com/hubfs/2026%20Agentic%20Coding%20Trends%20Report.pdf)
- [Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [Measuring AI Agent Autonomy in Practice](https://www.anthropic.com/research/measuring-agent-autonomy)
- [Building Effective AI Agents](https://www.anthropic.com/research/building-effective-agents)
- [Property-Based Testing with Claude (NeurIPS 2025)](https://red.anthropic.com/2026/property-based-testing/)
- [Challenges in Evaluating AI Systems](https://www.anthropic.com/research/evaluating-ai-systems)

### Industry Context
- [Anthropic: Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)
- [MCP donated to Agentic AI Foundation](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation)
- [Gene Kim — The Three Developer Loops](https://itrevolution.com/articles/the-three-developer-loops-a-new-framework-for-ai-assisted-coding/)
- [Simon Willison — Not Vibe Coding](https://simonwillison.net/2025/May/1/not-vibe-coding/)
- [Cline Plan & Act paradigm](https://cline.bot/blog/plan-smarter-code-faster-clines-plan-act-is-the-paradigm-for-agentic-coding)
- [Aider lint and test docs](https://aider.chat/docs/usage/lint-test.html)
- [Kiro: Beyond Vibe Coding](https://kiro.dev/blog/introducing-kiro/)
- [Open SWE (LangChain)](https://blog.langchain.com/introducing-open-swe-an-open-source-asynchronous-coding-agent/)

---

*Evolving Intelligence AI — evolvingintelligence.ai*
*"AI coding agents aren't dumb. They're blind. And blindness isn't fixed by smarter neurons. It's fixed by better sensors."*
*"And even when they can see, they skip the check. That's not fixed by better prompts. It's fixed by better gates."*
