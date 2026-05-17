# State Management Reference

## PROJECT_STATE.md Schema

This file is the single source of truth for project progress. It is created during initialization
and updated after every stage transition.

### Full Template

```markdown
# Project State: {project_name}

## Project Info
- **Name**: {project_name}
- **Dev Model**: {Waterfall|Incremental|Iterative|Spiral|Scrum}
- **Tech Stack**: {frontend} / {backend} / {database}
- **Created**: {date}
- **Current Version**: {v1 for first cycle, v2+ for subsequent}
- **Last Updated**: {date}

## Current Status
- **Current Cycle**: {cycle number or name, e.g., "Increment 2" or "Sprint 3"}
- **Current Stage**: {stage number and name, e.g., "3 - Architecture"}
- **Current Role**: {active role name}
- **Overall Progress**: {X}%

## Version History

### v1 — {cycle name}
- **Scope**: {what v1 delivered}
- **Status**: {completed|in_progress}
- **Deployed**: {date, or "pending"}
- **Archive**: `.archive/v1/` (after deployment)
- **Key Specs**: prd.md (baseline), architecture.md (baseline)

{Add v2+ entries after each version deploys.}
### v2 — {cycle name}
- **Scope**: {from .spec/next-version-input.md}
- **Status**: {in_progress}

## Cycle Plan
{For Waterfall, this is a single cycle. For others, list planned cycles.}

### Cycle: {cycle name}
- **Scope**: {what features/modules this cycle covers}
- **Status**: {planned|in_progress|completed}

## Stage Status

| Stage | Role | Status | Deliverables |
|-------|------|--------|-------------|
| 0. Init | Project Manager | completed | CLAUDE.md, docs/project-charter.md |
| 1. Requirements | Product Manager | completed | .spec/prd.md |
| 2. UI Design | UI Designer | in_progress | - |
| 3. Architecture | Tech Architect | pending | - |
| 4. Database | DB Engineer | pending | - |
| 5. Backend | Backend Engineer | pending | - |
| 6. Frontend | Frontend Engineer | pending | - |
| 7. Code Review | Tech Architect | pending | docs/code-review-report.md |
| 8. Environment Deploy | DevOps Engineer | pending | devops/ (varies by method), devops/README.md |
| 9a. Testing & Audit | Test + Security Engineer | pending | .spec/test-cases.md, docs/test-report.md, docs/security-audit.md |
| 9b. Bug Fix & Regression | Test Engineer | pending | - (skipped if no Critical/High bugs) |
| 10. UAT | Product Manager | pending | docs/user-guide.md, docs/release-notes.md, docs/uat-report.md, .spec/next-version-input.md |
| 11. Production Deploy | DevOps Engineer | pending | devops/ci.yml, docs/deployment-guide.md |

## Role Switch Log

| Timestamp | From | To | Reason | Resolution |
|-----------|------|----|--------|-----------|
| | | | | |

## Environment Capabilities

- **Detected at**: {date}
- {tool_name}: ✅ 可用（Stage {N} {usage}）
- {tool_name}: ❌ 未安装（{fallback}）

{Populated by the Environment Detection SubAgent during Stage 0. Read references/env_detection.md.}

## Key Decisions Log

| Stage | Decision | Choice | Rationale |
|-------|----------|--------|-----------|
| Init | Dev Model | Incremental | Multiple independent modules |

## Risk Register

| Risk | Probability | Impact | Mitigation | Owner |
|------|------------|--------|------------|-------|
| | | | | |

## Deliverables Index

| Stage | File Path | Description |
|-------|-----------|-------------|
| 0 | CLAUDE.md | Auto-generated AI project instructions |
| 0 | docs/project-charter.md | Project charter |
| 1 | .spec/prd.md | Product requirements document |

## Role Context Guide

This section is updated every stage transition. It tells the current role exactly what to read,
in priority order. This is the primary mechanism for progressive context loading.

### Current Role: {role name} — Stage {N}: {stage name}

**Must Read** (read these before starting work):
1. `{file path}` — {one-line description of what to look for in this file}
2. `{file path}` — {one-line description}

**Handoff Note** (concise summary from previous role):
- `.spec/handoffs/stage{N-1}_{name}.md` — {one-line summary of what the previous role communicated}

**Reference** (read relevant sections when you encounter specific questions):
3. `{file path}` Section {N} — {what this section covers, when to read it}
4. `{file path}` — {what this covers}

**Skip** (not needed for this role):
- `{file path}` — {reason it's not relevant}

**Subdirectory CLAUDE.md** (auto-loaded when you access the directory):
- `{dir}/CLAUDE.md` — {what conventions it contains}

**If you need comprehensive review** (use subagent):
- Spawn a subagent to read all `.spec/` files and produce a concise summary
- Only needed for roles that require cross-cutting understanding (e.g., Test Engineer)
```

### Role Context Guide Examples

Below are the typical guides for each role. These are starting points — the actual guide is
dynamically generated based on which stages have been completed.

**Stage 1 — Product Manager:**
```
Must Read:
1. CLAUDE.md — Tech stack and project conventions
2. docs/project-charter.md — Project vision and constraints

Reference: (none — first working role)
Reference (HTML): references/html_report_template.md — When generating prd.html
Skip: (none yet)
```

**Stage 2 — UI Designer:**
```
Must Read:
1. .spec/prd.md — User stories and feature requirements that need UI
Handoff: .spec/handoffs/stage1_requirements.md
Reference:
2. CLAUDE.md — Tech stack (may affect UI framework choice)
Reference (HTML): references/html_report_template.md — When generating ui-design.html
Skip: (none yet)
```

**Stage 3 — Architect:**
```
Must Read:
1. .spec/prd.md — Business Module Map (Section 6) is the primary input for module decomposition
2. .spec/ui-design.md — Page count, component complexity, dynamic content needs
Handoff: .spec/handoffs/stage2_ui_design.md
Reference:
3. docs/project-charter.md — Scale expectations, integration requirements
Reference (HTML): references/html_report_template.md — When generating architecture.html
Skip: (none)
```

**Stage 4 — DB Engineer:**
```
Must Read:
1. .spec/api-spec.md — Data structures revealed by API request/response schemas
2. .spec/architecture.md — Module design and data flow (focus Section 5)
Handoff: .spec/handoffs/stage3_architecture.md
Reference:
3. .spec/prd.md — Scale expectations, data volume (Section 5)
Reference (HTML): references/html_report_template.md — When generating database.html
Skip: .spec/ui-design.md — No database implications
```

**Stage 5 — Backend Engineer:**
```
Must Read:
1. .spec/api-spec.md — Endpoints to implement, request/response formats
2. database/schema.sql — Table structures to query against
Handoff: .spec/handoffs/stage4_database.md
Reference:
3. .spec/architecture.md Section 2 — Module boundaries (if unclear on scope)
4. .spec/database.md Section 4 — Index strategy (for complex queries)
Subdirectory: backend/CLAUDE.md — Coding conventions (auto-loaded)
Skip: .spec/prd.md, .spec/ui-design.md — Already reflected in API spec
```

**Stage 6 — Frontend Engineer:**
```
Must Read:
1. .spec/ui-design.md — Page layouts, design system, interaction flows
2. .spec/api-spec.md — Available endpoints and data contracts
Handoff: .spec/handoffs/stage5_backend.md (API deviations from spec)
Reference:
3. .spec/prd.md — User stories for acceptance verification
Subdirectory: frontend/CLAUDE.md — Component conventions (auto-loaded)
Skip: .spec/architecture.md, .spec/database.md — Backend concerns
```

**Stage 7 — Tech Architect (Code Review):**
```
Must Read:
1. .spec/architecture.md — Architecture design to review against
2. .spec/api-spec.md — API contracts to verify conformance
Handoff: .spec/handoffs/stage5_backend.md + stage6_frontend.md
Reference:
3. backend/README.md + frontend/README.md — Runtime/build requirements
Reference (HTML): references/html_report_template.md — When generating code-review.html
Subagent: Fork parallel review SubAgents for backend and frontend code
Skip: .spec/prd.md, .spec/ui-design.md — Already reflected in code via API spec
```

**Stage 8 — DevOps Engineer (Environment Deploy):**
```
Must Read:
1. .spec/architecture.md — Deployment architecture, tech stack
2. backend/README.md + frontend/README.md — Runtime/build requirements
3. .spec/handoffs/stage7_codereview.md — Code review findings (may affect deployment)
Reference:
4. database/seed_data.sql — Test data to pre-load
Subdirectory: devops/CLAUDE.md — Deployment conventions (auto-loaded)
Skip: .spec/ui-design.md, .spec/prd.md — Not relevant for deployment
Goal: User can start the app using chosen deployment method
```

**Stage 9a — Test Engineer + Security Engineer:**
```
Must Read:
1. devops/README.md — How to start the application (PREREQUISITE: app must be running)
2. .spec/api-spec.md — API contracts to test against
3. .spec/test-cases.md — Test case specifications
Handoff: .spec/handoffs/stage7_codereview.md + stage8_deploy.md
Reference:
4. .spec/prd.md — Acceptance criteria for verification
5. .spec/ui-design.md — Interaction flows for UI testing
6. references/subagent_patterns.md Scenario F — Bug fix SubAgent workflow
Reference (HTML): references/html_report_template.md — When generating test-report.html / security-audit.html
Subagent recommended: Spawn subagent to read all .spec/ files and produce
  a "Test Scope Summary" to avoid loading everything into main context
Bug Fix: When Critical/High bugs found, coordinate fix via Scenario F SubAgents
  (pre-check → fix → regression). Do NOT exit Stage 9a until all blocking bugs resolved.
  If Critical/High bugs exist, proceed to Stage 9b.
Skip: (depends on findings)
```

**Stage 9b — Test Engineer (Bug Fix & Regression):**
```
Must Read:
1. docs/test-report.md — Bug reports from Stage 9a
2. .spec/handoffs/stage9a_testing.md — Testing handoff with bug list
Reference:
3. references/subagent_patterns.md Scenario F — Bug fix SubAgent workflow
Skip: .spec/prd.md, .spec/ui-design.md — Focus on bug fixes only
Note: This stage is SKIPPED if Stage 9a finds no Critical/High bugs.
```

**Stage 10 — Product Manager (UAT):**
```
Must Read:
1. .spec/prd.md — Original requirements, acceptance criteria, out-of-scope items
2. .spec/handoffs/stage8_deploy.md — Startup guide, test accounts, access URLs
3. devops/README.md — How to start the application
Reference:
4. docs/project-charter.md — Long-term vision for next version planning
5. .spec/architecture.md — Scalability considerations for next version input
6. .spec/ui-design.md — UI design for user guide step descriptions
Reference (HTML): references/html_report_template.md — When generating uat.html
Skip: database/schema.sql — No DB work in UAT
Note: Use startup method from devops/README.md. Product Manager returns for this stage.
```

**Stage 11 — DevOps Engineer (Production Deploy):**
```
Must Read:
1. .spec/handoffs/stage10_uat.md — UAT sign-off, deployment-related feedback
2. Existing devops/ configs — Deployment configs from Stage 8
Reference:
3. docs/security-audit.md — Security recommendations for infrastructure
4. .spec/architecture.md — Deployment architecture (if production differs)
Subdirectory: devops/CLAUDE.md — Deployment conventions (auto-loaded)
Skip: .spec/ui-design.md, .spec/prd.md — Not relevant for deployment
```

## Update Rules

## Update Rules

### When a stage completes
1. Set the stage status to `completed`
2. Add all produced deliverables to the Deliverables Index
3. The current role writes a handoff note to `.spec/handoffs/stage{N}_{name}.md`
4. Set the next stage status to `in_progress`
5. Update `Current Stage` and `Current Role`
6. **Update the Role Context Guide** for the next role (must-read, reference, skip)
7. Update `Last Updated` date
8. Recalculate `Overall Progress`
9. **Display session recommendation** — suggest the user start a new Claude Code session
   for the next stage (see Session Management in SKILL.md). This is optional — the user
   may choose to continue in the same session.

### When a cycle completes (version deployment)
1. Set all stages in the cycle to `completed`
2. Update Version History: set current version status to `completed`, add deployed date
3. **Archive version snapshots** — move handoffs, version-specific docs to `.archive/v{N}/`
   (see `references/version_transition.md` for full archive operation)
4. **Evolve baseline specs** — add version markers to `.spec/` baseline files
5. If more cycles remain:
   a. Create the next version's entry in Version History
   b. Create the next cycle's row in Cycle Plan
   c. Reset stage statuses for the new cycle (all `pending` except Stage 0 which is `in_progress`)
   d. Update `Current Version` and `Current Cycle`
   e. Reset Role Context Guide for v{N+1} Stage 0 (streamlined init)
   f. Reset Deliverables Index for the new cycle
6. Keep Environment Capabilities (re-detect in new Stage 0)
7. Keep Key Decisions Log (append new decisions below existing ones)
8. **Strongly recommend a new session** — the current context is full of v{N} decisions

### When an exception switch occurs
1. Set current stage status to `interrupted`
2. Add entry to Role Switch Log with reason
3. Set the target role as active
4. When the exception is resolved:
   - Set the exception role's work as complete
   - Restore the interrupted stage to `in_progress`
   - Update the Role Switch Log resolution column

### When a stage is interrupted (mid-execution recovery)

If PROJECT_STATE.md shows a stage as `in_progress` but the previous session ended before
completion (e.g., user exited mid-stage, error occurred), use this recovery procedure:

1. **Detect interruption**: When resuming and the current stage is `in_progress`, check if
   any deliverable files from this stage already exist on disk.

2. **Resume from checkpoint**:
   - Scan the Deliverables Index for the current stage — check which files already exist
   - List found deliverables to the user for confirmation: "以下文件已存在，是否正确？"
   - If user confirms existing files are correct → skip those deliverables, continue from
     the next unfinished step
   - If user says files are incorrect → treat as if starting the stage fresh

3. **Update state**: After recovery confirmation, update PROJECT_STATE.md with a note in
   the Key Decisions Log: "Stage {N} resumed after interruption, {X} deliverables confirmed"

4. **Session recommendation**: Always suggest a new session after detecting an interruption —
   the previous session's context is lost and may contain stale decisions.

This mechanism ensures no work is lost when a session ends unexpectedly, and the resuming
role can pick up where the previous attempt left off without re-doing completed work.

### Progress Calculation
```
Overall Progress = (completed_stages / total_stages) × 100

For multi-cycle projects:
Overall Progress = ((completed_cycles × stages_per_cycle + completed_stages_in_current_cycle) /
                    (total_cycles × stages_per_cycle)) × 100
```

### When rolling back to a previous stage
1. Set the target stage and all subsequent stages to `pending`
2. Remove deliverable entries for the affected stages
3. Delete or rename the affected deliverable files (add `_archived_` prefix)
4. Set the target stage to `in_progress`
5. Update `Current Stage` and `Current Role`
6. Add an entry to Key Decisions Log noting the rollback reason
7. **Strongly recommend a new session** — the current context likely contains decisions
   and references based on the rolled-back deliverables, which can confuse the resumed stage.
   A new session reads the updated PROJECT_STATE.md cleanly.
