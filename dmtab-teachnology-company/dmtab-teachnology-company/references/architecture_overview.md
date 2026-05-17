# Architecture Overview Reference

This file contains detailed architecture information that is loaded on demand, not on every
skill invocation. SKILL.md contains only pointers to this file.

## Project Directory Structure

All deliverables are written under the project root directory (determined at initialization).
The project uses a clear separation between **AI-consumed specifications** and **human documentation**:

- `.spec/` — Development constraint specs consumed by AI coding agents (what to build)
- `docs/` — Human-facing documentation (how to use/deploy/maintain)
- `CLAUDE.md` — Project-level AI instructions, auto-generated at initialization

```
{project_root}/
├── .doc/                       # HTML visual reports (human-facing, browser-viewable)
│   └── html_report/
│       ├── prd.html            # PRD visual report
│       ├── ui-design.html      # UI design report
│       ├── architecture.html   # Architecture report
│       ├── database.html       # Database design report (ER diagrams)
│       ├── code-review.html    # Code review report
│       ├── test-report.html    # Test report
│       ├── security-audit.html # Security audit report
│       └── uat.html            # UAT report
├── CLAUDE.md                    # Auto-generated AI project instructions
├── PROJECT_STATE.md             # Single source of truth (always maintained)
├── .spec/                       # AI constraint specs — read by coding agents
│   ├── prd.md                   # Product requirements
│   ├── ui-design.md             # UI design spec
│   ├── architecture.md          # System architecture
│   ├── api-spec.md              # API specification
│   ├── database.md              # Database design
│   ├── test-cases.md            # Test cases
│   └── handoffs/                # Role handoff notes (progressive context)
│       ├── stage1_requirements.md
│       ├── stage2_ui_design.md
│       ├── stage3_architecture.md
│       ├── stage4_database.md
│       ├── stage5_backend.md
│       ├── stage6_frontend.md
│       ├── stage7_codereview.md
│       ├── stage8_deploy.md
│       ├── stage9a_testing.md
│       ├── stage9b_bugfix.md
│       ├── stage10_uat.md
│       └── stage11_production.md
├── .claude/rules/               # Path-scoped rules (auto-triggered by Claude Code)
│   ├── api-standards.md         # Triggered when editing controllers
│   ├── db-conventions.md        # Triggered when editing database files
│   ├── frontend-rules.md        # Triggered when editing frontend files
│   └── spec-reminder.md         # Triggered when editing .spec/ files
├── docs/                        # Human-facing documentation (中文)
│   ├── project-charter.md       # 项目章程
│   ├── deployment-guide.md      # 部署指南
│   ├── security-audit.md        # 安全审计报告
│   └── test-report.md           # 测试总结报告
├── database/
│   ├── CLAUDE.md                # Lazy-loaded DB conventions
│   ├── schema.sql               # DDL scripts
│   ├── seed_data.sql            # Seed data
│   └── data_dict.md             # Data dictionary
├── backend/
│   ├── CLAUDE.md                # Lazy-loaded backend rules
│   └── ...                      # Structure varies by tech stack
├── frontend/
│   ├── CLAUDE.md                # Lazy-loaded frontend rules
│   └── ...                      # Structure varies by tech stack
└── devops/
    ├── CLAUDE.md                # Lazy-loaded DevOps rules
    ├── Dockerfile
    ├── docker-compose.yml
    └── ci.yml
```

## Progressive Context Loading (Five Layers)

Late-stage roles (backend engineer, test engineer) would otherwise need to read 6-7 previous
spec files, consuming massive context. The five-layer mechanism prevents this:

**Layer 1 — Role Context Guide** (in PROJECT_STATE.md): Tells the current role exactly which
files are must-read, reference, or skippable. Always read first.

**Layer 2 — Handoff Notes** (in `.spec/handoffs/`): Concise summaries written by the previous
role, containing only the information the next role needs. Replaces reading full spec files.

**Layer 3 — Subdirectory CLAUDE.md** (in `backend/`, `frontend/`, `database/`, `devops/`):
Automatically lazy-loaded by Claude Code when the role accesses files in that directory.
Contains role-specific coding conventions and the subset of specs relevant to that directory.

**Layer 4 — Path-scoped rules** (in `.claude/rules/`): Automatically triggered by Claude Code
when editing files matching the path pattern. Injects just-in-time reminders (e.g., "when
editing controllers, follow the API spec").

**Layer 5 — Subagent aggregation** (for roles that need comprehensive review): When a role
needs to understand the full system (e.g., test engineer), spawn a subagent with the task
"read all .spec/ files and produce a concise scope summary." The subagent uses its own context
window and returns only the summary to the main conversation.

## Subagent Mechanism

Claude Code's Fork SubAgent creates an isolated context window that shares the parent's context
prefix, maximizing prompt cache hits. Use it to keep the main conversation clean during heavy
file operations. Read `references/subagent_patterns.md` for the full pattern definitions.

### Parallel Strategy Assessment (Stage 3)

Before any parallel SubAgent is activated, the architect performs a **Parallel Strategy Assessment**
during Stage 3. This assessment uses four dimensions — module count, dependency tiers, API surface,
and module independence — to recommend one of four strategies:

| Strategy | Condition | SubAgent Scenarios |
|----------|-----------|-------------------|
| Sequential | < 3 modules | None |
| Scenario A | 3-4 modules, > 15 endpoints | Frontend-Backend parallel |
| Scenario A+B | >= 5 modules, >= 2 Tier-1 modules | Full parallel |
| Scenario A Only | >= 5 modules, single dependency chain | Frontend-Backend parallel |

The assessment is recorded in `.spec/architecture.md` Section 3. The architect recommends,
the user decides. Read `references/subagent_patterns.md` for the full decision matrix.

### When to Use Subagent

| Scenario | Stage | Trigger Condition | Priority |
|----------|-------|-------------------|----------|
| A. Backend + Frontend parallel | 5+6 | API spec is frozen, user approves parallel | P0 |
| B. Module-level parallel | 5 | Project has 5+ modules with dependency graph | P1 |
| C. Test scope aggregation | 9a | Test engineer needs comprehensive system understanding | P1 |
| D. Security code scanning | 9a | Security engineer needs to scan all source code | P1 |
| E. Deployment config generation | 8 | DevOps needs to generate deployment configs | P2 |
| F. Bug fix coordination | 9a+9b | Critical/High bugs found during testing | P0 |

### Consistency Safeguards for Parallel Execution

**Scenario A (Frontend + Backend parallel)**:
1. Freeze `.spec/api-spec.md` before forking — mark with `<!-- FROZEN -->` comment
2. Both SubAgents read the same frozen spec as their source of truth
3. After both complete, fork a **Verification SubAgent** to cross-check implementations
4. Fix inconsistencies in main conversation based on verification report
5. Remove FROZEN marker when done

**Scenario B (Module-level parallel)**:
1. Generate **shared layer** first (entities, response wrapper, middleware) via single SubAgent
2. Architecture must define **module dependency graph** and **internal interface contracts**
3. Execute modules by tier — Tier 1 (no deps) parallel, then Tier 2, etc.
4. After all tiers complete, fork an **Integration Verification SubAgent**
5. Fix dependency issues in main conversation based on verification report

### When NOT to Use Subagent

- Stages with heavy user interaction (initialization, requirements, UI design)
- Simple, focused tasks (database DDL, single-file edits)
- Tasks where the main conversation needs full file details to continue

## Role Switching

### Normal Sequential Handoff
When a stage completes and the user confirms, switch to the next stage's role automatically.
The new role reviews the just-completed deliverables as part of their context loading.

### Exception Switching
Sometimes issues arise that require jumping to a different role out of order:

| Trigger | Switch To | Reason |
|---------|-----------|--------|
| Test finds design flaw | UI Designer or Architect | Need design/architecture adjustment |
| Dev finds requirements unclear | Product Manager | Need requirement clarification |
| DB design impacts performance | DB Engineer + Architect | Joint review needed |
| Security audit finds vulnerability | Security Engineer + relevant dev | Security fix required |
| Deployment environment mismatch | DevOps + Architect | Deployment strategy adjustment |
| Any stage identifies schedule risk | Project Manager | Risk assessment and replanning |

When an exception switch occurs:
1. Save current stage state in PROJECT_STATE.md (mark as `interrupted`)
2. Log the switch reason in `role_switch_log`
3. Enter the target role, explain why you're here out of sequence
4. After the exception is resolved, automatically return to the interrupted stage
