# SWE Kit Reference Analysis Report

> Analysis of 6 AI coding agent kits in `.ref/` — workflow, features, architecture, and comparison matrix.

---

## Kit Summaries

### 1. ClaudeKit Engineer

**Purpose:** Production-ready boilerplate for building software with AI-powered CLI coding agents (Claude Code, OpenCode). Solves the integration gap between LLM capabilities and real-world SDLC workflows.

**Workflow:** Research → Plan → Implement → Test → Review → Document → Deploy. Planner spawns parallel researchers, synthesizes findings into phased plans, then hands off to executor → tester → reviewer → docs-manager → git-manager.

**Key Features:**
- 14 specialized agents (Planner, Researcher, Fullstack-Dev, Code-Reviewer, Tester, Debugger, UI/UX-Designer, Code-Simplifier, Docs-Manager, Git-Manager, Project-Manager, Journal-Writer, MCP-Manager, Brainstormer)
- 54 slash commands, 33 skills covering AI/ML, backend, frontend, DevOps, payments, databases
- File-based agent communication via `./plans/reports/`
- Parallel multi-agent research (3+ researchers per plan)
- 10+ hooks (scout-block, privacy-block, session-init, post-edit-simplify, dev-rules-reminder)
- Multi-provider notifications (Telegram, Discord, Slack)
- Statusline with context/token/cost metrics
- Experimental agent teams for multi-session orchestration

**Architecture:** Layered — Commands → Agent Orchestration → 14 Agents → 33 Skills → Hooks & Middleware → Configuration. All state in `.claude/` directory.

**Language/Runtime:** Node.js 18+, MIT license, v2.15.1

---

### 2. GET SHIT DONE (GSD)

**Purpose:** Meta-prompting and context engineering system that solves **context rot** — quality degradation as AI fills its context window. Shifts complexity from user workflow to system architecture.

**Workflow:** New Project (~30 min deep questioning) → Discuss Phase → Plan Phase (research → synthesize → plan → verify) → Execute Phase (atomic per-task commits) → Verify Work → Ship (GitHub PR).

**Key Features:**
- 69 commands covering full project lifecycle
- 24 specialized agents (researchers, synthesizer, planner, executor, verifier, auditors, debuggers)
- Wave-based execution (auto-group independent tasks for parallelism)
- Scope reduction detection (blocks planner from silently dropping decisions)
- Schema drift detection (catches missing migrations)
- 4 quality gates (pre-flight, revision, escalation, abort)
- User behavioral profiling (USER-PROFILE.md)
- 9 runtime hooks (statusline, context-monitor, prompt-guard, workflow-guard)
- Thinking model support (o3/o4-mini/Gemini 2.5 Pro patterns)
- 130+ test files, 70% coverage target

**Architecture:** 5-layer — Commands (69) → Workflows (68) → Agents (24) → CLI Tools (gsd-tools.cjs, 8.5K LOC) → File System (.planning/). Fresh context per agent eliminates degradation.

**Language/Runtime:** Node.js, MIT license, v1.34.2

---

### 3. OpenSpec

**Purpose:** AI-native spec-driven development that adds a lightweight spec layer as a contract between human intent and AI implementation. Designed for brownfield and greenfield projects alike.

**Workflow:** `/opsx:propose` (create change + all artifacts) → `/opsx:apply` (implement tasks) → `/opsx:archive` (merge into source of truth). Expanded path available: new → ff → continue → verify → sync → archive.

**Key Features:**
- 10 core workflows as skills + commands (propose, explore, new, continue, apply, ff, sync, archive, bulk-archive, verify)
- 24+ AI assistant support (Claude Code, Cursor, Windsurf, Copilot, Cline, Continue, Amazon Q, RooCode, Kiro, and more)
- Dual delivery modes (skills + commands)
- Profile system (core = 4 workflows, custom = pick subset)
- Delta-based specs (ADDED/MODIFIED/REMOVED for brownfield)
- Artifact validation engine
- Project context injection via config.yaml
- Auto-detection of existing tool directories
- Cross-platform (macOS, Linux, Windows with shell completions)

**Architecture:** TypeScript CLI (130 .ts files) — CLI (Commander.js) → Commands → Core (artifact-graph, validation, parsers, templates) → Command adapters (24+ tools). State in `openspec/` directory.

**Language/Runtime:** TypeScript/Node.js, 68 test files

---

### 4. Skills (Khuym)

**Purpose:** Validate-first workflow for agentic software development. Prevents ambiguous requests from becoming expensive rework through a nine-skill gated chain.

**Workflow:** Using-Khuym → Exploring (→ GATE 1: CONTEXT approved) → Planning → Validating (→ GATE 2: execute?) → Swarming → Executing → Reviewing (→ GATE 3: P1 fixed?) → Compounding.

**Key Features:**
- 9 core chain skills + 3 support + 5 standalone
- CONTEXT.md as single source of truth for locked decisions
- Beads + bv: dependency-aware task graph with PageRank, critical path analysis
- Agent Mail: file reservations, async messaging, multi-agent coordination
- 3 working modes (small_change, standard_feature, high_risk_feature)
- 5-specialist parallel review (code-quality, architecture, security, test-coverage, learnings)
- Compounding loop: automatic learnings capture feeds future planning
- Scout command for quick orientation
- Hooks + guardrails (session-start, pre-tool-use, end-of-turn)

**Architecture:** Codex plugin (v2.4.1) — Skills (SKILL.md with YAML frontmatter) → Hooks (.codex/) → State (.khuym/) → External tools (beads, bv, gkg, Agent Mail MCP). Inspired by Flywheel, GSD, Compound Engineering, Superpowers.

**Language/Runtime:** Node.js 18+, Codex-first

---

### 5. Spec Kit

**Purpose:** Implements Spec-Driven Development (SDD) — specifications as the executable source of truth. Code becomes the generated output; maintaining software means evolving specifications.

**Workflow:** Initialize → Define Constitution → Create Specification → Clarify Ambiguities → Plan Implementation → Execute Tasks. Each phase generates versioned artifacts in feature branches (001-feature-name).

**Key Features:**
- 25+ AI agent support (Claude Code, Gemini, Copilot, Cursor, Qwen, opencode, Codex, Windsurf, Junie, Kilo, Auggie, and more)
- Template-driven specification (constrains LLM output toward quality)
- Constitutional enforcement (immutable architectural principles as gates)
- Auto-numbered feature branching (sequential or timestamp)
- Extension system (30+ community extensions)
- Preset system (pre-configured project templates)
- 9 core slash commands (/speckit.specify, .clarify, .constitution, .plan, .analyze, .tasks, .implement, .checklist)
- Bidirectional lifecycle hooks (before_specify, after_plan, etc.)
- Parallel task markers [P] for safe concurrent execution

**Architecture:** Python CLI (typer, rich, click, pyyaml) — Templates → Shell Scripts (bash + PowerShell) → Agent Config → Extension Framework → Constitution. State in `.specify/` directory.

**Language/Runtime:** Python, pyproject.toml, uv for dependency management

---

### 6. Superpowers

**Purpose:** Behavior-shaping framework that makes poor development practices technically impossible through mandatory skill activation and multi-stage verification. Evidence-driven development: test-first, systematic debugging, verification before claims.

**Workflow:** Brainstorming (Socratic design) → Git Worktrees → Writing Plans (bite-sized 2-5 min tasks) → Subagent-Driven Development (fresh agent per task + two-stage review) → TDD (RED-GREEN-REFACTOR) → Code Review → Finishing Branch.

**Key Features:**
- 14 skills (brainstorming, TDD, systematic-debugging, writing-plans, subagent-driven-development, executing-plans, git-worktrees, parallel-agents, code-review, verification-before-completion, finishing-branch, writing-skills)
- Mandatory workflow enforcement (skills aren't suggestions)
- Two-stage review loops (spec compliance → code quality)
- Subagent isolation (fresh context per task, zero history)
- TDD for skills themselves (RED-GREEN-REFACTOR)
- 94% PR rejection rate (quality over volume)
- Zero dependencies (pure markdown skills)
- Multi-platform (Claude Code, Cursor, Codex, OpenCode, Copilot CLI, Gemini)
- Visual companion for brainstorming (browser-based mockups)

**Architecture:** Flat skill namespace — Skills (14 SKILL.md files) → 1 Agent Template (code-reviewer) → Hooks (SessionStart) → Commands (deprecated). Zero npm dependencies.

**Language/Runtime:** Shell/Markdown only, MIT license, v5.0.7

---

## Comparison Matrix

### Core Identity

| Dimension | ClaudeKit Engineer | GET SHIT DONE | OpenSpec | Skills (Khuym) | Spec Kit | Superpowers |
|---|---|---|---|---|---|---|
| **Primary Focus** | Full SDLC orchestration | Context engineering | Spec-as-contract | Validate-first workflow | Spec-driven development | Behavior shaping |
| **Philosophy** | Multi-agent specialization | Eliminate context rot | Lightweight alignment | Lock decisions early | Specs are source of truth | Make bad practices impossible |
| **Target User** | Teams building products | Solo devs, large projects | Brownfield + greenfield | High-risk/cross-team work | Enterprise/multi-agent | Discipline-focused devs |
| **Language** | Node.js | Node.js | TypeScript | Node.js (Codex) | Python | Shell/Markdown |
| **Dependencies** | Minimal (boilerplate) | gsd-tools.cjs | Commander.js, 130 .ts files | beads, bv, gkg, Agent Mail | typer, rich, click, pyyaml | Zero |

### Scale & Complexity

| Dimension | ClaudeKit Engineer | GET SHIT DONE | OpenSpec | Skills (Khuym) | Spec Kit | Superpowers |
|---|---|---|---|---|---|---|
| **Commands** | 54 | 69 | 10 workflows | 9 chain + extras | 9 slash commands | 14 skills |
| **Agents** | 14 | 24 | N/A (tool-agnostic) | 5 review + swarming | N/A (template-driven) | 1 (code-reviewer) |
| **Skills** | 33 | N/A (commands) | 10 | 17 | N/A (templates) | 14 |
| **Hooks** | 10+ | 9 | 3 (pre/post/postinstall) | 3 (session/pre-tool/stop) | Lifecycle hooks | 1 (SessionStart) |
| **Test Files** | 75 tests | 130+ files | 68 files | Script-based | pytest suite | Bash integration |
| **AI Tool Support** | 2 (Claude, OpenCode) | 12 runtimes | 24+ assistants | Codex-first | 25+ agents | 6 platforms |

### Workflow Characteristics

| Dimension | ClaudeKit Engineer | GET SHIT DONE | OpenSpec | Skills (Khuym) | Spec Kit | Superpowers |
|---|---|---|---|---|---|---|
| **Entry Point** | `/ck:plan` or `/ck:cook` | `/gsd-new-project` | `/opsx:propose` | `khuym:using-khuym` | `/speckit.specify` | Brainstorming skill |
| **Planning Depth** | 7-phase plans | Wave-based + dependency graphs | Artifact dependency graph | Beads + bv graph analytics | Template-driven phases | Bite-sized 2-5 min tasks |
| **Execution Model** | Sequential agent chain | Atomic per-task commits | Task checkbox tracking | Swarming (parallel workers) | Parallel task markers [P] | Subagent per task |
| **Quality Gates** | Agent-driven review | 4 gates (pre-flight→abort) | Artifact validation | 3 human gates + validation | Checklist-based gates | Two-stage review loops |
| **State Storage** | `./plans/reports/` | `.planning/` (MD/JSON) | `openspec/` | `.khuym/` + `.beads/` | `.specify/` + branches | Git worktrees |
| **Context Management** | Scout-block filtering | Fresh agent per task | Project config injection | CONTEXT.md + HANDOFF.json | Constitution enforcement | Subagent isolation |

### Unique Differentiators

| Dimension | ClaudeKit Engineer | GET SHIT DONE | OpenSpec | Skills (Khuym) | Spec Kit | Superpowers |
|---|---|---|---|---|---|---|
| **Killer Feature** | 33 domain skills library | Context rot elimination | 24+ tool support | Beads graph + Agent Mail | Constitutional enforcement | TDD-everything culture |
| **Innovation** | Parallel multi-agent research | Scope reduction detection | Delta specs (brownfield) | Compounding learnings loop | Template-driven LLM constraint | Skills tested with TDD |
| **Automation** | Notifications (Telegram/Discord/Slack) | User behavioral profiling | Auto-detect tool directories | Scout quick-orientation | Extension ecosystem (30+) | Visual brainstorming companion |
| **Scaling Strategy** | Agent teams (experimental) | Sub-projects for large work | Profile system (core/custom) | 3 working modes | Feature branch numbering | Plan decomposition |

### Strengths & Gaps Summary

| Kit | Top Strength | Notable Gap |
|---|---|---|
| **ClaudeKit Engineer** | Broadest skill coverage (33 domains) | Limited to 2 AI tools (Claude, OpenCode) |
| **GET SHIT DONE** | Most mature context management | Single-user focused, no team coordination |
| **OpenSpec** | Widest AI tool support (24+) | No built-in agent orchestration |
| **Skills (Khuym)** | Strongest coordination (beads + Agent Mail) | Requires external tool dependencies |
| **Spec Kit** | Largest agent ecosystem (25+) + extensions (30+) | LLM-dependent, no deterministic fallback |
| **Superpowers** | Zero dependencies, purest discipline | Minimal customization, only 1 agent template |

---

## Design Pattern Comparison

### How Each Kit Handles Key Concerns

| Concern | ClaudeKit | GSD | OpenSpec | Khuym | Spec Kit | Superpowers |
|---|---|---|---|---|---|---|
| **"What should I build?"** | Brainstormer agent | 30-min deep questioning | `/opsx:explore` | Exploring skill (Socratic) | Constitution + specify | Brainstorming (design doc) |
| **"How do I plan it?"** | Planner + 3 researchers | Research → synthesize → plan | Proposal + artifact graph | Planning + beads graph | Template-driven plan | Writing-plans (bite-sized) |
| **"How do I verify the plan?"** | Code-reviewer agent | 3-iteration verification loop | Artifact validation | Validating gate (8 dimensions) | Checklist + analyze | Verification-before-completion |
| **"How do I execute?"** | Executor agent chain | Atomic per-task commits | `/opsx:apply` task list | Swarming + executing workers | `/speckit.implement` | Subagent-driven development |
| **"How do I prevent mistakes?"** | Scout-block + privacy hooks | 4 quality gates | Spec validation engine | 3 human gates + Agent Mail | Constitutional gates | Mandatory TDD + two-stage review |
| **"How do I learn?"** | Journal-writer agent | User behavioral profiling | Archive + sync specs | Compounding loop | Bidirectional feedback | N/A (implicit via TDD) |
| **"How do I ship?"** | Git-manager + CI/CD | `/gsd-ship` (auto PR) | `/opsx:archive` | Reviewing gate → merge | Feature branch workflow | Finishing-a-development-branch |

---

## Architectural Spectrum

```
Lightweight ◄──────────────────────────────────────────────► Heavyweight

Superpowers    OpenSpec    Spec Kit    Khuym    ClaudeKit    GSD
(0 deps)       (CLI)      (Python)    (Codex)  (Node.js)    (8.5K LOC tools)
14 skills      10 wkflows  9 commands  17 skills 54 commands  69 commands
Markdown-only  TypeScript  Templates   Beads+MCP Shell+Py     Node.js runtime
```

```
Opinionated ◄──────────────────────────────────────────────► Flexible

Superpowers    Khuym      GSD        ClaudeKit   Spec Kit    OpenSpec
(mandatory     (3 gates)  (4 gates)  (agent      (extension  (profile system,
 TDD, 94%                             chain)      ecosystem)  pick workflows)
 rejection)
```

```
Spec-First ◄───────────────────────────────────────────────► Code-First

Spec Kit       OpenSpec    Khuym      Superpowers  GSD        ClaudeKit
(specs ARE     (spec       (CONTEXT   (design doc  (planning  (plan then
 source of     layer)      .md lock)   → TDD)      artifacts)  cook)
 truth)
```
