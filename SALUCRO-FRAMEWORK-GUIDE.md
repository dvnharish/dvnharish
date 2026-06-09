# Salucro Backend Code Assist — Complete Guide

> What the setup script does, how the framework works, and how to invoke every agent and workflow.

---

## Table of Contents

1. [What the Script Does](#1-what-the-script-does)
2. [How to Run the Script](#2-how-to-run-the-script)
3. [What Gets Created](#3-what-gets-created)
4. [The Four Slash Commands](#4-the-four-slash-commands)
5. [The Three Workflows](#5-the-three-workflows)
6. [All 32 Agents — Responsibilities and Inputs/Outputs](#6-all-32-agents)
7. [The GCJ Review Cycle](#7-the-gcj-review-cycle)
8. [Governance Gates](#8-governance-gates)
9. [Artifact Directory Map](#9-artifact-directory-map)
10. [Environment Variables](#10-environment-variables)
11. [Running Agents Individually](#11-running-agents-individually)
12. [End-to-End Example](#12-end-to-end-example)

---

## 1. What the Script Does

`setup-salucro-backend-code-assist.sh` is a **one-shot installer** that stamps an enterprise multi-agent SDLC framework on top of any existing Spring Boot project. You run it once at the project root — it creates every configuration file, agent definition, workflow script, rule, skill, template, script, and documentation file needed to turn Jira tickets into production-ready Java code using Claude Code subagents.

### What it installs in one run

| Category | Count | What |
|----------|-------|------|
| Agent definitions | 32 | `.claude/agents/*.md` — one file per specialist |
| Slash commands (skills) | 4 | `.claude/skills/*/SKILL.md` |
| Orchestration workflows | 3 | `.claude/workflows/*.js` |
| Context-aware rules | 4 | `.claude/rules/*.md` |
| Utility scripts | 3 | `scripts/*.sh` |
| Templates | 2 | `templates/*.md` |
| Examples | 2 | `examples/*.yaml / *.md` |
| Governance docs | 1 | `docs/contracts-and-governance/GOVERNANCE.md` |
| Config files | 3 | `CLAUDE.md`, `.mcp.json`, `.claude/settings.json` |

### Script behaviour

```bash
# Safe mode — skips any file that already exists
bash setup-salucro-backend-code-assist.sh

# Force mode — overwrites everything
FORCE=true bash setup-salucro-backend-code-assist.sh
```

The script is **idempotent by default**. Running it twice without `FORCE=true` will skip every file that was already written, so you can safely re-run it after updating to a newer version of the framework without losing any customisations.

---

## 2. How to Run the Script

### Prerequisites

| Tool | Minimum version | Purpose |
|------|----------------|---------|
| Claude Code CLI | latest | Runs agents and workflows |
| Java | 21 | Spring Boot compilation and tests |
| Gradle | 8.x | Build and test runner (`./gradlew`) |
| Node.js + npx | 18+ | OpenAPI lint (`@redocly/cli`) |
| Atlassian API token | — | Jira MCP + Confluence MCP |

### Step 1 — Copy the script to your project root

```bash
cp setup-salucro-backend-code-assist.sh /path/to/your-spring-boot-project/
cd /path/to/your-spring-boot-project/
```

### Step 2 — Set required environment variables

```bash
export ATLASSIAN_BASE_URL="https://your-org.atlassian.net"
export ATLASSIAN_USER_EMAIL="your.email@company.com"
export ATLASSIAN_API_TOKEN="your-atlassian-api-token"
```

> Get your Atlassian API token at: https://id.atlassian.com/manage-profile/security/api-tokens

### Step 3 — Run the installer

```bash
bash setup-salucro-backend-code-assist.sh
```

You will see `[INFO]` lines as each file is written. The final output lists everything created.

### Step 4 — Open the project in Claude Code

```bash
claude .
```

The framework is now active. Type `/plan PROJ-123` to start your first planning run.

---

## 3. What Gets Created

```
your-spring-boot-project/
│
├── README.md                              Framework overview and flowchart
├── CLAUDE.md                              Claude Code project instructions
├── .mcp.json                              Atlassian MCP server config (Jira + Confluence)
│
├── .claude/
│   ├── settings.json                      Model (Opus 4.6) + permission allow/deny list
│   │
│   ├── rules/                             Auto-loaded context rules
│   │   ├── governance.md                  Always loaded — 6 mandatory gates
│   │   ├── spring-boot.md                 Loaded for src/**/*.java
│   │   ├── testing.md                     Loaded for src/test/**/*.java
│   │   └── openapi.md                     Loaded for docs/api-contracts/**/*.yaml
│   │
│   ├── agents/                            32 subagent definitions
│   │   ├── master-orchestrator.md
│   │   ├── workflow-router.md
│   │   ├── verdict-handler.md
│   │   ├── planner.md
│   │   ├── jira-mcp.md
│   │   ├── requirement-extraction.md
│   │   ├── sdlc-plan.md
│   │   ├── architecture.md
│   │   ├── hld.md
│   │   ├── lld.md
│   │   ├── api-contract.md
│   │   ├── database-design.md
│   │   ├── sequence-diagram.md
│   │   ├── pseudocode.md
│   │   ├── confluence-mcp.md
│   │   ├── entity.md
│   │   ├── dto.md
│   │   ├── mapper.md
│   │   ├── exception.md
│   │   ├── config.md
│   │   ├── repository.md
│   │   ├── service.md
│   │   ├── controller.md
│   │   ├── test-generator.md
│   │   ├── unit-test.md
│   │   ├── integration-test.md
│   │   ├── contract-test.md
│   │   ├── architecture-review.md
│   │   ├── security-review.md
│   │   ├── performance-review.md
│   │   ├── critic.md
│   │   └── judge.md
│   │
│   ├── skills/                            Slash commands
│   │   ├── plan/SKILL.md                  /plan
│   │   ├── new-feature/SKILL.md           /new-feature
│   │   ├── review-code/SKILL.md           /review-code
│   │   └── show-plan/SKILL.md             /show-plan
│   │
│   └── workflows/                         JS orchestration workflows
│       ├── workflow-plan.js               Workflow P (design only)
│       ├── workflow-b-review.js           Workflow B (GCJ code review)
│       └── workflow-c-full-plan.js        Workflow C (full SDLC)
│
├── docs/
│   ├── contracts-and-governance/
│   │   └── GOVERNANCE.md
│   ├── sdlc-plans/                        Agent outputs per plan
│   ├── design-docs/                       HLD, LLD, architecture decisions
│   ├── api-contracts/                     OpenAPI YAML files
│   ├── database-designs/                  DB design + DDL
│   ├── sequence-diagrams/                 Mermaid flow diagrams
│   ├── sdlc-reviews/                      GCJ findings and critiques
│   └── sdlc-verdicts/                     Judge verdicts
│
├── scripts/
│   ├── check-coverage.sh                  JaCoCo 80% gate checker
│   ├── validate-openapi.sh                Redocly lint runner
│   └── new-plan-id.sh                     Generate SAL-<ID>-<TIMESTAMP>
│
├── examples/
│   ├── sample-jira-extraction.yaml        Reference output from @jira-mcp
│   └── sample-verdict.md                  Reference verdict document
│
└── templates/
    ├── verdict-template.md                Blank verdict template for @judge
    └── sdlc-plan-template.md              Blank SDLC plan template for @sdlc-plan
```

---

## 4. The Four Slash Commands

Type these directly in Claude Code chat. They map to skills in `.claude/skills/`.

### `/plan <JIRA-TICKET-ID>`

Runs the full **design-only pipeline** (Workflow P). No code is written.

```
/plan PROJ-123
```

What happens:
1. `@jira-mcp` reads the Jira ticket → `jira-extraction.yaml`
2. `@requirement-extraction` expands to MoSCoW requirements → `requirements.md`
3. `@sdlc-plan` builds the master SDLC plan → `plan.md`
4. `@architecture` writes ADRs → `architecture-decisions.md`
5. `@hld` writes C4 diagrams + NFR table → `hld.md`
6. `@lld` writes the 11-section design blueprint → `lld.md`
7. In parallel: `@api-contract` → `openapi.yaml`, `@database-design` → `db-design.md`, `@sequence-diagram` → `sequence.md`, `@pseudocode` → `pseudocode.md` (HIGH/VERY_HIGH only)
8. GCJ design review: 3 specialists → @critic → @judge (max 2 cycles)
9. If approved: `@confluence-mcp` publishes all docs to Confluence

**Output:** All approved design artifacts in `docs/`, plan status in Jira, pages in Confluence.

---

### `/new-feature <JIRA-TICKET-ID>`

Runs the **full SDLC pipeline** (Workflow C). Calls `/plan` first internally, then generates all code from the approved plan artifacts.

```
/new-feature PROJ-123
```

What happens:
1. Runs the complete `/plan` pipeline first (see above)
2. If design is approved, starts multi-agent code generation:
   - Foundation (parallel): `@entity`, `@dto`, `@exception`, `@config`
   - `@mapper` (after entities + DTOs exist)
   - `@repository`
   - TDD phase: `@test-generator` → `@unit-test` (writes FAILING tests)
   - `@service` (must make unit tests pass)
   - `@controller` (must match `openapi.yaml` exactly)
   - `@integration-test` + `@contract-test` (parallel)
3. Runs full test suite + coverage gate (`bash scripts/check-coverage.sh`)
4. GCJ code review: 3 specialists → `@critic` → `@judge` (max 2 cycles)
5. `@verdict-handler` transitions Jira + updates Confluence

**Output:** Production-ready Java code in `src/`, passing tests, GCJ verdict, updated Jira/Confluence.

---

### `/review-code`

Runs a **standalone GCJ code review** (Workflow B) on the current codebase without running the planning pipeline. Use this when you already have code and want quality gates applied.

```
/review-code
```

What happens:
1. 3 specialists run in parallel, each writing its own findings file:
   - `@architecture-review` → `arch-findings-N.md`
   - `@security-review` → `sec-findings-N.md`
   - `@performance-review` → `perf-findings-N.md`
2. `@critic` reads all three files, synthesises → `critique-N.md`
3. `@judge` reads ONLY `critique-N.md`, runs coverage gate → `verdict.md`
4. If `REQUEST_CHANGES`: agents fix issues, retry (max 2 total cycles)
5. `@verdict-handler` processes final verdict

**Output:** `docs/sdlc-reviews/` findings files, `docs/sdlc-verdicts/` verdict, Jira + Confluence updated.

---

### `/show-plan [planId]`

**Read-only status view** — no model invocation, no agents run. Shows the current state of a plan.

```
/show-plan
/show-plan SAL-PROJ-123-20250101120000
```

Displays:
- Plan ID, Jira ticket, workflow type, complexity rating
- Phase completion table (PENDING / IN_PROGRESS / COMPLETE)
- GCJ cycle count and last verdict per artifact type
- Next required action (which agent to call next)
- AC Traceability: which acceptance criteria have test coverage
- Open questions from `requirements.md`

---

## 5. The Three Workflows

These JavaScript files in `.claude/workflows/` are the orchestration backbone. They are called by the skills and can also be invoked directly from Claude Code.

### Workflow P — `workflow-plan.js`

**Purpose:** Design pipeline only. Produces approved, published design artifacts.

**Input:** `{ jiraTicket: "PROJ-123" }`

**Phases:**
```
Requirements  → jira-mcp, requirement-extraction, sdlc-plan
Architecture  → architecture
HLD           → hld
LLD           → lld
Contracts     → api-contract, database-design, sequence-diagram, pseudocode  [PARALLEL]
Design GCJ    → [arch+sec+perf specialists PARALLEL] → critic → judge  [up to 2 cycles]
Publish       → confluence-mcp  [only if APPROVE or APPROVED_WITH_MINOR_RISKS]
```

**Returns:** `{ designVerdict, designCycle }`

---

### Workflow B — `workflow-b-review.js`

**Purpose:** GCJ code review loop for existing code.

**Input:** `{ planId: "SAL-PROJ-123-...", targetFiles: "src/**" }`

**Phases (per cycle, max 2):**
```
Specialists   → [arch-review + sec-review + perf-review PARALLEL]
               Each writes own findings file (never shared before critic)
Criticize     → critic reads all 3 findings + runs own checklist → critique-N.md
Judge         → judge reads ONLY critique-N.md → verdict.md
               Step 1: coverage gate (bash scripts/check-coverage.sh)
               Step 2: any Critical issues? → REQUEST_CHANGES / REJECT
               Step 3: ≥3 Major issues? → REQUEST_CHANGES / REJECT
               Step 4: clean pass → APPROVE / APPROVED_WITH_MINOR_RISKS
Verdict       → verdict-handler (Jira + Confluence)
```

**Returns:** `{ codeVerdict, codeCycle, planId }`

---

### Workflow C — `workflow-c-full-plan.js`

**Purpose:** Complete SDLC — design then code, fully automated.

**Input:** `{ jiraTicket: "PROJ-123" }`

**How it works — key design principle:**

Workflow C calls `workflow-plan` as a **sub-workflow** first. It never re-derives requirements or re-designs anything. Every code agent receives the `planId` and reads exclusively from its pre-approved plan artifact section.

```
Planning         → workflow('workflow-plan', { jiraTicket })    ← sub-workflow
                   If designVerdict = REJECT → ABORT
Foundation       → [entity + dto + exception + config PARALLEL]
                   mapper, repository (sequential after foundation)
Tests First      → test-generator → unit-test (writes FAILING tests — TDD)
Implementation   → service (must make unit tests pass)
                   controller (must match openapi.yaml exactly)
Integration Tests→ [integration-test + contract-test PARALLEL]
                   run-tests + check-coverage.sh
Code GCJ         → workflow('workflow-b-review', { planId })    ← sub-workflow
```

**Returns:** `{ planId, designVerdict, designCycle, codeVerdict, codeCycle }`

---

## 6. All 32 Agents

### Group 1 — Orchestrators

| Agent | File | Role | Reads | Writes |
|-------|------|------|-------|--------|
| `@master-orchestrator` | `agents/master-orchestrator.md` | Entry point, generates Plan ID, routes to P/B/C | Jira ticket, user intent | Routes to workflows |
| `@workflow-router` | `agents/workflow-router.md` | Decision table for `/plan`, `/review-code`, `/new-feature` | User command | Dispatches workflow |
| `@verdict-handler` | `agents/verdict-handler.md` | Processes final judge verdict | `verdict.md` | Jira transition, Confluence update, notifications |

### Group 2 — Planners

| Agent | File | Role | Reads | Writes |
|-------|------|------|-------|--------|
| `@planner` | `agents/planner.md` | Three-gate SDLC plan gate-keeper | Jira MCP output | Gates: abort on failure |
| `@jira-mcp` | `agents/jira-mcp.md` | Reads Jira, extracts structured YAML | Jira ticket (MCP) | `docs/sdlc-plans/<planId>/jira-extraction.yaml` |
| `@requirement-extraction` | `agents/requirement-extraction.md` | MoSCoW, expand ACs, derive API surface | `jira-extraction.yaml` | `docs/sdlc-plans/<planId>/requirements.md` |
| `@sdlc-plan` | `agents/sdlc-plan.md` | Master plan with AC traceability matrix | `requirements.md` | `docs/sdlc-plans/<planId>/plan.md` |

### Group 3 — Design Agents

| Agent | File | Role | Reads | Writes |
|-------|------|------|-------|--------|
| `@architecture` | `agents/architecture.md` | ADRs, Clean Architecture, DDD boundaries | `requirements.md` + `plan.md` | `docs/design-docs/<planId>.architecture-decisions.md` |
| `@hld` | `agents/hld.md` | C4 Level 1+2 diagrams, NFR table, tech stack | `requirements.md` + `architecture-decisions.md` | `docs/design-docs/<planId>.hld.md` |
| `@lld` | `agents/lld.md` | **Master design blueprint** — 11 sections, each consumed by exactly one code agent | `hld.md` + `requirements.md` | `docs/design-docs/<planId>.lld.md` |
| `@api-contract` | `agents/api-contract.md` | OpenAPI 3.1 spec, lint-validated | `lld.md` §10 Controller + §4 DTO | `docs/api-contracts/<planId>.openapi.yaml` |
| `@database-design` | `agents/database-design.md` | ER diagram, DDL, Flyway migrations, PII classification | `lld.md` §3 Entity Design | `docs/database-designs/<planId>.db-design.md` |
| `@sequence-diagram` | `agents/sequence-diagram.md` | Mermaid flows — 6 per endpoint (happy + 400/401/404/422/500) | `lld.md` + `openapi.yaml` | `docs/sequence-diagrams/<planId>.sequence.md` |
| `@pseudocode` | `agents/pseudocode.md` | Service algorithm pseudocode (HIGH/VERY_HIGH complexity only) | `lld.md` §9 Service Layer | `docs/sdlc-plans/<planId>/pseudocode.md` |
| `@confluence-mcp` | `agents/confluence-mcp.md` | Publish approved docs to Confluence | `design-verdict.md` (must be APPROVED) | Confluence pages + publication log |

#### LLD's 11 Sections and Their Consumers

The LLD (`lld.md`) is the single source of truth for code generation. Each section feeds exactly one agent — no agent reads another agent's section.

```
§1  Package Structure     →  @entity (reference)
§2  Class Diagrams        →  all code agents (reference)
§3  Entity Design         →  @entity (exclusive source)
§4  DTO Design            →  @dto (exclusive source)
§5  Mapper Design         →  @mapper (exclusive source)
§6  Exception Hierarchy   →  @exception (exclusive source)
§7  Configuration Section →  @config (exclusive source)
§8  Repository Layer      →  @repository (exclusive source)
§9  Service Layer         →  @service (exclusive source)
§10 Controller Layer      →  @controller reads openapi.yaml instead
§11 Test Class Design     →  @test-generator + @unit-test
```

### Group 4 — Code Generation Agents

| Agent | File | Reads Exclusively From | Writes To |
|-------|------|----------------------|-----------|
| `@entity` | `agents/entity.md` | `db-design.md` § Table Definitions | `src/.../domain/entity/` |
| `@dto` | `agents/dto.md` | `lld.md` §4 DTO Design | `src/.../application/dto/` |
| `@mapper` | `agents/mapper.md` | `lld.md` §5 Mapper Design | `src/.../application/mapper/` |
| `@exception` | `agents/exception.md` | `lld.md` §6 Exception Hierarchy | `src/.../domain/exception/` + `GlobalExceptionHandler.java` |
| `@config` | `agents/config.md` | `lld.md` §7 Configuration + `openapi.yaml` (title/version) | `src/.../infrastructure/config/` |
| `@repository` | `agents/repository.md` | `lld.md` §8 Repository Layer | `src/.../infrastructure/persistence/` |
| `@service` | `agents/service.md` | `lld.md` §9 Service Layer + `pseudocode.md` (if HIGH) | `src/.../application/service/` |
| `@controller` | `agents/controller.md` | `openapi.yaml` — every path, operationId, schema | `src/.../infrastructure/web/controller/` |

**Key rules enforced by agent definitions:**
- `@entity` — extends `BaseEntity`, `@Where(deleted_at IS NULL)`, `@SQLDelete`, `@Version`, factory methods only (no public constructors)
- `@dto` — Java 21 records, exact Bean Validation from LLD, `PagedResponse<T>` wrapper
- `@mapper` — `unmappedTargetPolicy = ERROR`, ignores `id/createdAt/updatedAt/version/deletedAt` on `toEntity`
- `@service` — `@Transactional` on writes, `readOnly=true` on reads, returns DTOs (never entities), publishes domain events
- `@controller` — no business logic, account ID from JWT claims only (`token.getToken().getClaimAsString("account_id")`), `@Valid` on every request body

### Group 5 — Testing Agents

| Agent | File | Reads | Writes |
|-------|------|-------|--------|
| `@test-generator` | `agents/test-generator.md` | `requirements.md` (all ACs) + `lld.md` §11 | `docs/sdlc-plans/<planId>/test-plan.md` |
| `@unit-test` | `agents/unit-test.md` | `test-plan.md` + `lld.md` §11 | `src/test/.../service/*Test.java` (FAILING — TDD) |
| `@integration-test` | `agents/integration-test.md` | `openapi.yaml` + `requirements.md` | `src/test/.../integration/*IT.java` |
| `@contract-test` | `agents/contract-test.md` | `openapi.yaml` | `src/test/resources/contracts/` + `*ContractTest.java` |

**TDD order enforced by workflow-c-full-plan.js:**
```
Phase 3 (Tests First):  test-generator → unit-test (FAILING)
Phase 4 (Implementation): service  ←— must make tests pass
                          controller ←— must match openapi.yaml
```

### Group 6 — GCJ Review Agents

| Agent | File | Role | Reads | Writes |
|-------|------|------|-------|--------|
| `@architecture-review` | `agents/architecture-review.md` | Checks package structure, DDD, Spring Boot compliance, OpenAPI match | Generated code + `lld.md` | `docs/sdlc-reviews/<planId>.arch-findings-N.md` |
| `@security-review` | `agents/security-review.md` | OWASP Top 10 (2021) A01–A10 check | Generated code + `openapi.yaml` | `docs/sdlc-reviews/<planId>.sec-findings-N.md` |
| `@performance-review` | `agents/performance-review.md` | N+1 queries, unbounded lists, missing readOnly, index gaps | Generated code + `db-design.md` | `docs/sdlc-reviews/<planId>.perf-findings-N.md` |
| `@critic` | `agents/critic.md` | **Synthesise** all three findings files into one ranked critique | All three `*-findings-N.md` files + code | `docs/sdlc-reviews/<planId>.critique-N.md` |
| `@judge` | `agents/judge.md` | **Binding verdict** (reads ONLY critique) | `critique-N.md` ONLY | `docs/sdlc-verdicts/<planId>.verdict.md` |

---

## 7. The GCJ Review Cycle

GCJ stands for **Generate → Criticize → Judge**. It is the quality gate applied to both design artifacts (inside Workflow P) and generated code (Workflow B / end of Workflow C).

### Why this structure?

Specialists run independently — they cannot see each other's findings before writing their own. This prevents one reviewer from anchoring others. The Critic then synthesises everything, and the Judge only ever reads the synthesis — not the raw specialist reports. This creates a clean separation between discovery (specialists), synthesis (critic), and decision (judge).

### Cycle flow

```
          Cycle N  (max 2 cycles total)
          ─────────────────────────────────────────────────────────
          │                                                       │
          ▼         [PARALLEL]                                    │
  @architecture-review ──→ arch-findings-N.md                    │
  @security-review     ──→ sec-findings-N.md     [specialists]   │
  @performance-review  ──→ perf-findings-N.md                    │
          │                                                       │
          ▼                                                       │
  @critic reads all 3 findings + re-reads code/design            │
  @critic writes critique-N.md  (ranked: Critical/Major/Minor)   │
  @critic does NOT issue a verdict                               │
          │                                                       │
          ▼                                                       │
  @judge reads ONLY critique-N.md  (never raw findings)          │
  @judge Step 1: bash scripts/check-coverage.sh  ← hard gate     │
  @judge Step 2: any Critical?  → REQUEST_CHANGES or REJECT      │
  @judge Step 3: ≥3 Majors?    → REQUEST_CHANGES or REJECT      │
  @judge Step 4: clean pass    → APPROVE / APPROVED_WITH_MINOR   │
  @judge writes verdict.md                                       │
          │                                                       │
          ├── APPROVE ──────────────────────────────────────────→ END
          ├── APPROVED_WITH_MINOR_RISKS ───────────────────────→ END
          ├── REQUEST_CHANGES (cycle < 2) ──────────────────────┘
          └── REJECT (cycle 2 OR coverage < 80%) ──────────────→ Human escalation
```

### Verdict meanings

| Verdict | Meaning | What happens |
|---------|---------|-------------|
| `APPROVE` | No critical/major issues; coverage ≥ 80% | Merge allowed; Jira → Done; Confluence updated |
| `APPROVED_WITH_MINOR_RISKS` | Minor issues accepted; coverage ≥ 80% | Merge allowed; risks logged to tech debt backlog |
| `REQUEST_CHANGES` | Critical or Major issues found; cycle < 2 | Agents fix specific `File:Line` issues; cycle repeats |
| `REJECT` | Cycle 2 still has criticals, OR coverage < 80% | Jira → BLOCKED; tech lead assigned; no further automation |

---

## 8. Governance Gates

Six hard gates are enforced by `.claude/rules/governance.md` (always loaded) and the workflow scripts.

| Gate | Rule | Enforced by |
|------|------|------------|
| **Jira-First** | No design or code starts without `jira-extraction.yaml` | `@planner` |
| **Design-Before-Code** | No code agent runs without APPROVED design verdict | `workflow-c-full-plan.js` checks `designVerdict` |
| **Plan-First Implementation** | Each code agent reads ONLY its designated plan artifact section | Agent definitions + architecture-review finding |
| **TDD Order** | `@unit-test` runs before `@service`/`@controller` | `workflow-c-full-plan.js` phase ordering |
| **Coverage ≥ 80%** | `@judge` Step 1 runs `bash scripts/check-coverage.sh` — hard fail | `@judge` agent + `workflow-b-review.js` |
| **Confluence Approval** | `@confluence-mcp` aborts if verdict ≠ APPROVE or APPROVED_WITH_MINOR_RISKS | `@confluence-mcp` agent |

### Prohibited actions (from `governance.md`)

- Publishing unapproved content to Confluence
- Code generation before design approval
- Writing `@service` or `@controller` before `@unit-test`
- `@judge` reading raw specialist findings directly (must go through `@critic`)
- Hardcoding account ID — must always come from JWT claims
- Business logic in controllers
- `@Transactional` on repository or controller layer
- `git push --force` (blocked in `settings.json`)

---

## 9. Artifact Directory Map

All artifacts land under `docs/`. The Plan ID (`SAL-<JIRA-ID>-<YYYYMMDDHHMMSS>`) namespaces all files for a given feature, so multiple features can be in-flight simultaneously.

```
docs/
├── sdlc-plans/
│   └── SAL-PROJ-123-20250101120000/
│       ├── jira-extraction.yaml       @jira-mcp output
│       ├── requirements.md            @requirement-extraction output
│       ├── plan.md                    @sdlc-plan output
│       ├── test-plan.md               @test-generator output
│       ├── pseudocode.md              @pseudocode output (HIGH/VERY_HIGH only)
│       └── confluence-publication.log @confluence-mcp log
│
├── design-docs/
│   ├── SAL-PROJ-123-20250101120000.architecture-decisions.md
│   ├── SAL-PROJ-123-20250101120000.hld.md
│   └── SAL-PROJ-123-20250101120000.lld.md
│
├── api-contracts/
│   └── SAL-PROJ-123-20250101120000.openapi.yaml
│
├── database-designs/
│   └── SAL-PROJ-123-20250101120000.db-design.md
│
├── sequence-diagrams/
│   └── SAL-PROJ-123-20250101120000.sequence.md
│
├── sdlc-reviews/
│   ├── SAL-PROJ-123-20250101120000.arch-findings-1.md
│   ├── SAL-PROJ-123-20250101120000.sec-findings-1.md
│   ├── SAL-PROJ-123-20250101120000.perf-findings-1.md
│   └── SAL-PROJ-123-20250101120000.critique-1.md
│
└── sdlc-verdicts/
    ├── SAL-PROJ-123-20250101120000.design-verdict.md
    └── SAL-PROJ-123-20250101120000.verdict.md
```

---

## 10. Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `ATLASSIAN_BASE_URL` | Yes | Your Atlassian org URL, e.g. `https://mycompany.atlassian.net` |
| `ATLASSIAN_USER_EMAIL` | Yes | Email address associated with the API token |
| `ATLASSIAN_API_TOKEN` | Yes | API token from https://id.atlassian.com/manage-profile/security/api-tokens |

These are used by `.mcp.json` to configure the Jira and Confluence MCP servers that `@jira-mcp` and `@confluence-mcp` call.

---

## 11. Running Agents Individually

You do not have to run a full workflow. Any agent can be called directly from the Claude Code chat by mentioning it with `@` and giving it context. This is useful for:
- Retrying a single step that failed
- Re-running a specific phase with corrected inputs
- Exploring what an agent would produce without committing to the full workflow

### Calling an agent directly

```
@jira-mcp Read ticket PROJ-456 and write jira-extraction.yaml for plan SAL-PROJ-456-20250101120000
```

```
@lld Read docs/design-docs/SAL-PROJ-123-20250101120000.hld.md and generate the LLD.
```

```
@judge Read docs/sdlc-reviews/SAL-PROJ-123-20250101120000.critique-1.md and issue a verdict.
       Run bash scripts/check-coverage.sh first as Step 1.
```

```
@entity Read docs/database-designs/SAL-PROJ-123-20250101120000.db-design.md
        ## Table Definitions section only. Generate JPA entities.
```

### Useful single-agent invocations

| Task | Invocation |
|------|-----------|
| Check what a Jira ticket says | `@jira-mcp Read PROJ-456` |
| Re-generate LLD from existing HLD | `@lld Read <planId>.hld.md and regenerate the LLD` |
| Lint the OpenAPI spec | `bash scripts/validate-openapi.sh docs/api-contracts/<planId>.openapi.yaml` |
| Check test coverage | `bash scripts/check-coverage.sh` |
| Generate a new Plan ID | `bash scripts/new-plan-id.sh PROJ-456` |
| Create plan directory | `CREATE_DIR=true bash scripts/new-plan-id.sh PROJ-456` |
| Show plan status | `/show-plan SAL-PROJ-123-20250101120000` |
| Run GCJ on specific files | `@architecture-review Review src/main/java/service/ against lld.md §9` |

### Running a specific workflow directly in Claude Code

You can also call workflows by name without going through a skill:

```
Run workflow 'workflow-plan' with args { jiraTicket: "PROJ-456" }
```

```
Run workflow 'workflow-b-review' with args { planId: "SAL-PROJ-456-20250101120000" }
```

---

## 12. End-to-End Example

**Scenario:** Build a Payment Methods API for Jira ticket `FINPAY-88`.

### Step 1 — Design only (to review before committing to code)

```
/plan FINPAY-88
```

The framework will:
1. Extract structured requirements from FINPAY-88 in Jira
2. Run all design agents (architecture → HLD → LLD → parallel contracts)
3. Run GCJ design review (up to 2 cycles)
4. If approved, publish to Confluence and return the planId

Example planId: `SAL-FINPAY-88-20250601093045`

### Step 2 — Review the design artifacts

```
/show-plan SAL-FINPAY-88-20250601093045
```

Or browse the files directly:
- `docs/design-docs/SAL-FINPAY-88-20250601093045.lld.md` — the main design blueprint
- `docs/api-contracts/SAL-FINPAY-88-20250601093045.openapi.yaml` — the API contract
- `docs/sdlc-verdicts/SAL-FINPAY-88-20250601093045.design-verdict.md` — design review verdict

### Step 3 — Generate code from the approved design

```
/new-feature FINPAY-88
```

> **Note:** If `/plan` was already run and the design is approved, `/new-feature` will re-use the existing design artifacts and skip straight to code generation.

The framework will:
1. Detect the approved design for `FINPAY-88`
2. Generate foundation code (entities, DTOs, exceptions, config) in parallel
3. Write FAILING unit tests (TDD)
4. Generate service layer (must make tests pass)
5. Generate controller (must match `openapi.yaml` exactly)
6. Run integration and contract tests
7. Run GCJ code review (up to 2 cycles)
8. Transition Jira to Done and update Confluence

### Step 4 — If a review cycle returns REQUEST_CHANGES

The agents automatically fix the specific `File:Line` issues identified in the verdict and re-run the GCJ cycle. If cycle 2 still has Critical issues, you get a `REJECT` verdict and the ticket is marked `BLOCKED` in Jira for human review.

Check what happened:
```
/show-plan SAL-FINPAY-88-20250601093045
```

Read the verdict:
```
cat docs/sdlc-verdicts/SAL-FINPAY-88-20250601093045.verdict.md
```

### Step 5 — Review code on an existing codebase (without /plan)

If you already have code and just want a quality check:

```
/review-code
```

This runs Workflow B in isolation — no planning, no Jira extraction, just GCJ review of whatever is currently in `src/`.

---

## Quick Reference Card

```
Command                    What it does
──────────────────────────────────────────────────────────────────────────
/plan PROJ-123             Design pipeline → Confluence publish
/new-feature PROJ-123      /plan + code gen + GCJ review (full SDLC)
/review-code               GCJ code review on existing src/
/show-plan [planId]        Status view (no agent invoked)

Scripts
──────────────────────────────────────────────────────────────────────────
bash scripts/check-coverage.sh          JaCoCo 80% coverage gate
bash scripts/validate-openapi.sh [path] Redocly lint on OpenAPI YAML
bash scripts/new-plan-id.sh PROJ-123    Generate SAL-PROJ-123-<timestamp>

Key artifact files (replace <pid> with your planId)
──────────────────────────────────────────────────────────────────────────
docs/sdlc-plans/<pid>/requirements.md          Source of truth for requirements
docs/design-docs/<pid>.lld.md                  Source of truth for code design
docs/api-contracts/<pid>.openapi.yaml          Source of truth for API contract
docs/database-designs/<pid>.db-design.md       Source of truth for entities
docs/sdlc-verdicts/<pid>.verdict.md            Latest GCJ verdict
```
