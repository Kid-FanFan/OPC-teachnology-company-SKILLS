# Stage Templates and Deliverables Reference

This file provides quick-reference templates for each stage's deliverables. Each role's
reference file has more detailed instructions; this file is for the skill engine to verify
that all expected deliverables are produced.

## Directory Convention

- **`.spec/`** — AI constraint specs. Consumed by coding agents during implementation.
- **`.spec/handoffs/`** — Concise role handoff notes for progressive context loading.
- **`docs/`** — Human-facing documentation. Read by stakeholders, ops, security reviewers.
- **`CLAUDE.md`** — Auto-generated project instructions. Read by AI agents every conversation.
- **`{dir}/CLAUDE.md`** — Lazy-loaded subdirectory rules. Loaded only when that dir is accessed.
- **`.claude/rules/`** — Path-scoped rules. Auto-triggered when matching files are edited.

## Stage 0: Initialization — Project Manager

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| CLAUDE.md | `CLAUDE.md` | Yes |
| Project Charter (中文) | `docs/project-charter.md` | Yes |
| Project State | `PROJECT_STATE.md` | Yes |
| Backend rules | `backend/CLAUDE.md` | Yes |
| Frontend rules | `frontend/CLAUDE.md` | Yes |
| Database rules | `database/CLAUDE.md` | Yes |
| DevOps rules | `devops/CLAUDE.md` | Yes |
| API rules | `.claude/rules/api-standards.md` | Yes |
| DB rules | `.claude/rules/db-conventions.md` | Yes |
| Frontend rules | `.claude/rules/frontend-rules.md` | Yes |
| Spec guard | `.claude/rules/spec-reminder.md` | Yes |

**Completion criteria**: All files exist. CLAUDE.md has tech stack and coding conventions.
Subdirectory CLAUDE.md files have framework-specific rules. Path-scoped rules reference
correct .spec/ files. PROJECT_STATE.md has valid initial state with Role Context Guide for
Stage 1.

**v2+ Streamlined Initialization**: When starting version 2 or later, Stage 0 is shorter.
The project charter, CLAUDE.md, directory structure, and rules already exist from v1.
The streamlined process has 3 rounds instead of 6:
1. **Scope Confirmation** — Read `.spec/next-version-input.md`, present synthesized scope, user confirms
2. **Technical Changes** — Any tech stack/model adjustments? Skip if unchanged
3. **Environment Re-detection** — Fork detection SubAgent, compare with previous results

In v2+, the Project Manager only needs to:
- Update `PROJECT_STATE.md` (Version History, Cycle Plan, reset Stage Status)
- Update `CLAUDE.md` if tech stack changed
- NOT regenerate project charter or directory structure
See `references/version_transition.md` for full v2+ initialization details.

## Stage 1: Requirements — Product Manager

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| PRD | `.spec/prd.md` | Yes |
| HTML Report | `.doc/html_report/prd.html` | Yes |
| Handoff Note | `.spec/handoffs/stage1_requirements.md` | Yes |

**PRD must include**:
- Overview (vision, target users, problem statement)
- Scope (in/out)
- User stories with acceptance criteria
- Feature priority matrix (MoSCoW)
- Non-functional requirements
- Business Module Map (module inventory, business flows, role permissions)
- Open questions

**Completion criteria**: PRD covers all features from project charter, user stories have
acceptance criteria, priority matrix is complete, business module map defines clear module
boundaries, user has approved.

## Stage 2: UI Design — UI Designer

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| UI Design Spec | `.spec/ui-design.md` | Yes |
| HTML Report | `.doc/html_report/ui-design.html` | Yes |
| Handoff Note | `.spec/handoffs/stage2_ui_design.md` | Yes |

**UI Design must include**:
- Page inventory
- Information architecture / navigation structure
- Design system (colors, typography, spacing, components)
- Page wireframes (layout, interactions, states per page)
- Interaction flows for key user journeys

**Completion criteria**: All pages from PRD are covered, design system is defined,
interaction flows map to user stories, user has approved.

## Stage 3: Architecture — Tech Architect

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| Architecture Spec | `.spec/architecture.md` | Yes |
| API Specification | `.spec/api-spec.md` | Yes |
| HTML Report | `.doc/html_report/architecture.html` | Yes |
| Handoff Note | `.spec/handoffs/stage3_architecture.md` | Yes |

**Architecture must include**:
- Architecture style and rationale
- Module decomposition
- Complete API specification (all endpoints)
- Cross-cutting concerns (auth, errors, logging, caching, security)
- Data flow diagrams
- Module dependency graph (required for projects with 5+ modules)
- Module internal interface contracts (required for parallel SubAgent execution)

**Completion criteria**: All features from PRD have API endpoints, modules are clearly
bounded, cross-cutting concerns are addressed, dependency graph is defined for large projects,
user has approved.

## Stage 4: Database — DB Engineer

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| DB Design Spec | `.spec/database.md` | Yes |
| Schema DDL | `database/schema.sql` | Yes |
| Data Dictionary | `database/data_dict.md` | Yes |
| Seed Data | `database/seed_data.sql` | Recommended |
| HTML Report | `.doc/html_report/database.html` | Yes |
| Handoff Note | `.spec/handoffs/stage4_database.md` | Yes |

**Completion criteria**: All entities from architecture are modeled, DDL is syntactically
valid, indexes are defined for query patterns, data dictionary is complete.

## Stage 5: Backend — Backend Engineer

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| Backend Source | `backend/` directory | Yes |
| Backend README | `backend/README.md` | Yes |
| Handoff Note | `.spec/handoffs/stage5_backend.md` | Yes |

**Backend must include**:
- Runnable application matching the tech stack
- All API endpoints from the API spec
- Database integration with the defined schema
- Authentication/authorization
- Error handling
- Setup instructions in README

**Completion criteria**: Application starts without errors, API endpoints match spec,
README explains how to run, user has reviewed key functionality.

**Subagent Support (optional)**:
- Scenario A: If frontend runs in parallel, freeze .spec/api-spec.md and fork two SubAgents.
  A Verification SubAgent checks consistency after both complete.
- Scenario B: If 5+ modules, use shared-layer-first + tier-based parallel execution.
  See `references/subagent_patterns.md` Scenarios A and B.

## Stage 6: Frontend — Frontend Engineer

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| Frontend Source | `frontend/` directory | Yes |
| Frontend README | `frontend/README.md` | Yes |
| Handoff Note | `.spec/handoffs/stage6_frontend.md` | Yes |

**Frontend must include**:
- Runnable application matching the tech stack
- All pages from UI design spec
- API integration
- Responsive layout
- Loading/error/empty states
- Setup instructions in README

**Completion criteria**: Application starts and renders, all pages are implemented,
API integration works, README explains how to run, user has reviewed the UI.

**Subagent Support (optional)**:
- Can run in parallel with Backend (Scenario A) when API spec is frozen.
  See `references/subagent_patterns.md` Scenario A.

## Stage 7: Code Review — Tech Architect

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| Code Review Report (中文) | `docs/code-review-report.md` | Yes |
| HTML Report | `.doc/html_report/code-review.html` | Yes |
| Handoff Note | `.spec/handoffs/stage7_codereview.md` | Yes |

**Purpose**: After development completes, the architect returns to review code quality and
architecture conformance before deployment. Catches issues that would be harder to fix
after the app is deployed and tested.

**Code Review must include**:
- Architecture conformance check (module boundaries, dependency rules)
- API contract verification (implemented vs. spec)
- Code quality assessment (error handling, security, performance, maintainability)
- Cross-stack consistency (frontend API calls vs. backend endpoints)
- Critical issues that must be fixed before deployment
- Recommendations for improvement

**Completion criteria**: All modules reviewed, API conformance verified, critical issues
resolved or acknowledged, code review report written, handoff note passed to DevOps.

**Subagent Support (optional)**:
- Fork parallel review SubAgents for backend and frontend code.
  See `references/subagent_patterns.md` Scenario E for SubAgent task format.

## Stage 8: Environment Deploy — DevOps Engineer

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| Deployment Configs | `devops/` (varies by method) | Yes |
| Startup Guide | `devops/README.md` | Yes |
| Handoff Note | `.spec/handoffs/stage8_deploy.md` | Yes |

**Purpose**: Deploy the application in a running environment for testing and user trial.
The DevOps engineer offers multiple deployment strategies and the user chooses.

**Deployment Options** (user selects):

| Method | Deliverables | Applicable When |
|--------|-------------|-----------------|
| Local Direct Run | `devops/start-local.sh`, `devops/env-setup.md` | Dev testing, quick verification |
| Docker Compose | `devops/Dockerfile`, `devops/docker-compose.yml`, `devops/.env.example` | Standard delivery, one-command startup |
| Kubernetes | `devops/k8s/` directory with manifests | Production-grade, autoscaling |
| Cloud-Managed | `devops/cloud/` directory with configs | AWS/Azure/GCP deployment |

**Startup Guide must include**:
- Deployment method chosen by user
- Prerequisites
- Startup commands
- Access URLs (frontend, backend API)
- Test accounts from seed data
- Stop commands
- Data persistence notes
- Common troubleshooting

**Completion criteria**: Application starts and is accessible, frontend communicates with
backend API, seed data is loaded, test accounts can log in, startup guide is clear.

**Subagent Support (optional)**:
- Scenario E: Fork a SubAgent to generate deployment configs.
  See `references/subagent_patterns.md` Scenario E.

## Stage 9a: Testing & Security Audit — Test Engineer + Security Engineer

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| Test Cases Spec | `.spec/test-cases.md` | Yes |
| Test Report (中文) | `docs/test-report.md` | Yes |
| Security Audit (中文) | `docs/security-audit.md` | Recommended |
| HTML Test Report | `.doc/html_report/test-report.html` | Yes |
| HTML Security Report | `.doc/html_report/security-audit.html` | Recommended |
| Handoff Note | `.spec/handoffs/stage9a_testing.md` | Yes |

**Prerequisite**: Application must be deployed and running (Stage 8 output).
Test engineer starts the app using `devops/README.md` instructions.

**Test Cases spec** (in `.spec/`) defines expected behavior for AI agents to verify against.
**Test Report** (in `docs/`) is the human-readable summary for QA sign-off.

**Test Report must include**:
- Execution summary (pass/fail counts)
- Bug reports with triple classification (severity + responsibility + decision type)
- Bug resolution results (fixed, deferred, plan)
- Pre-deployment checklist
- Risk assessment
- Recommendation (pass/conditional/fail)

**Security Audit must include**:
- Findings by severity
- OWASP category mapping
- Remediation guidance

**Bug Fix Workflow** (required when Critical/High bugs are found):
1. Triage: Classify each bug by severity, responsibility, and decision type
2. Pre-check: Fork clarification SubAgents for requirement ambiguity or architecture impact
3. Fix: Fork parallel fix SubAgents grouped by responsibility (backend, frontend, security)
4. Risk Assessment: Fork PM SubAgent if blocking bugs > 5 or fix time > 20% of cycle
5. Regression: Re-test fixed bugs and potentially affected features
6. Resolution: Document fixed vs deferred bugs with user acknowledgment
See `references/subagent_patterns.md` Scenario F for complete SubAgent templates.

**Completion criteria**: All user stories have test cases, ALL Critical/High bugs are fixed
and regression-verified, security review is done, deferred bugs are acknowledged by user,
recommendation is clear. If Critical/High bugs exist, proceed to Stage 9b.

**Subagent Support**:
- Scenario C: Fork a SubAgent to read all .spec/ files and produce a test scope summary.
- Scenario D: Fork parallel backend + frontend security scanning SubAgents.
- Scenario F: Fork SubAgents for pre-check, bug fix, and progress risk assessment.
  See `references/subagent_patterns.md` Scenarios C, D, and F.

## Stage 9b: Bug Fix & Regression — Test Engineer

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| Updated Test Report (中文) | `docs/test-report.md` | Yes |
| Handoff Note | `.spec/handoffs/stage9b_bugfix.md` | Yes |

**Purpose**: Fix Critical/High bugs found during Stage 9a and verify through regression testing.
This stage is skipped if all tests pass with no Critical/High bugs.

**Workflow**:
1. Fork fix SubAgents grouped by responsibility (backend, frontend, security)
2. Review and merge fixes
3. Re-execute affected test cases (regression verification)
4. Update test report with resolution results

**Completion criteria**: All Critical/High bugs are fixed and regression-verified.
Medium/Low bugs are recorded in backlog with user acknowledgment.

## Stage 10: User Acceptance Testing — Product Manager

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| User Guide (中文) | `docs/user-guide.md` | Yes |
| Release Notes (中文) | `docs/release-notes.md` | Yes |
| UAT Report (中文) | `docs/uat-report.md` | Yes |
| Next Version Input | `.spec/next-version-input.md` | Yes |
| HTML Report | `.doc/html_report/uat.html` | Yes |
| Handoff Note | `.spec/handoffs/stage10_uat.md` | Yes |

**Prerequisite**: Application is deployed (Stage 8) and tested (Stage 9a/9b).
PM uses `devops/README.md` to start the application for user trial.

**User Guide must include**:
- Quick start (how to start the application)
- Test accounts and seed data description
- Feature-by-feature walkthrough with operation steps and expected results
- Known limitations

**Release Notes must include**:
- Feature list with implementation status
- Known issues with severity and plan
- UAT sign-off status

**UAT Report must include**:
- Trial feedback (Bug / UX Issue / Feature Request)
- Processing results for each feedback item
- Bug fix results (if Critical/High bugs were fixed during UAT)
- Deferred items
- Next version suggestion summary

**Next Version Input must include**:
- Source 1: PRD deferred items (Out of Scope, Future Considerations, Won't Have)
- Source 2: Vision & scale expectations from project charter
- Source 3: UAT user feedback (deferred items)
- Source 4: Known issues backlog from Stage 9a
- Source 5: Architecture evolution from architecture spec
- Synthesized draft scope for next version

**Completion criteria**: Application starts successfully, user has tried all core features,
user guide covers all features, user signs off (accepts current version), next version input
synthesized from all five sources, handoff note written.

**UAT Bug Fix Workflow** (when Critical/High bugs found during user trial):
Lightweight version of Stage 9a's Scenario F — no pre-check SubAgents, max 1 regression round,
user re-verification required. PM coordinates fix SubAgents dispatched by responsibility group
(Backend / Frontend). Max 1 re-fork per bug; if still fails, defer to Backlog.
See `references/subagent_patterns.md` Scenario G for complete workflow and templates.

**Subagent Support**:
- Scenario G: Fork lightweight fix SubAgents for Critical/High bugs found during UAT.
- If minor UX fixes needed: Fork frontend SubAgent for adjustments.
  See `references/subagent_patterns.md` Scenario G.

## Stage 11: Production Deployment — DevOps Engineer

| Deliverable | File Path | Required |
|-------------|-----------|----------|
| CI/CD Config | `devops/ci.yml` | Recommended |
| Deployment Guide (中文) | `docs/deployment-guide.md` | Yes |
| Handoff Note | `.spec/handoffs/stage11_production.md` | Recommended |

**Purpose**: After UAT passes, extend Stage 8's deployment configs for production.
DevOps engineer returns to add production-specific configurations.

**Deployment guide must include**:
- Architecture overview
- Prerequisites
- Quick start (reference staging setup)
- Production environment configuration (env vars, secrets, HTTPS)
- Deployment procedures (initial deploy, update, rollback)
- CI/CD pipeline
- Monitoring and alerting
- Backup and recovery
- Troubleshooting

**Completion criteria**: Production deployment docs cover all procedures,
CI/CD pipeline configured, user confirms docs are sufficient or app is deployed.

**Subagent Support (optional)**:
- Scenario E: Fork a SubAgent to generate CI/CD and production configs.
  See `references/subagent_patterns.md` Scenario E.

## Cross-Stage Deliverable Tracker

After each stage, update PROJECT_STATE.md's Deliverables Index:

```
| Stage | File Path | Description | Type | Status |
|-------|-----------|-------------|------|--------|
| 0 | CLAUDE.md | AI project instructions | config | ✅ |
| 0 | docs/project-charter.md | Project charter | doc | ✅ |
| 0 | backend/CLAUDE.md | Backend lazy-loaded rules | config | ✅ |
| 0 | frontend/CLAUDE.md | Frontend lazy-loaded rules | config | ✅ |
| 0 | database/CLAUDE.md | Database lazy-loaded rules | config | ✅ |
| 0 | devops/CLAUDE.md | DevOps lazy-loaded rules | config | ✅ |
| 0 | .claude/rules/* | Path-scoped auto rules | config | ✅ |
| 1 | .spec/prd.md | Product requirements | spec | ✅ |
| 1 | .spec/handoffs/stage1_requirements.md | PM→UI handoff | handoff | ✅ |
| 2 | .spec/ui-design.md | UI design | spec | ✅ |
| 2 | .spec/handoffs/stage2_ui_design.md | UI→Arch handoff | handoff | ✅ |
| ... | ... | ... | ... | ... |
```

Mark status as ✅ when the file exists and has been approved by the user.
Type column: `spec` = .spec/ constraint, `doc` = docs/ human doc, `code` = source code,
`config` = configuration, `handoff` = progressive context note, `html_report` = visual HTML report.
