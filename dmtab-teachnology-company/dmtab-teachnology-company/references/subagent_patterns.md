# Subagent Patterns Reference

This file defines the patterns for using Claude Code's Fork SubAgent mechanism
within the DMTAB development workflow. Subagent provides isolated context windows
for heavy tasks, keeping the main conversation clean.

## Parallel Strategy Assessment

Before any SubAgent is forked, the architect must perform a Parallel Strategy Assessment
during Stage 3. The assessment determines which parallel pattern(s) to use — or whether
to skip parallelism entirely and develop sequentially.

### Assessment Dimensions

| Dimension | Metric | How to Measure |
|-----------|--------|---------------|
| Module count | Total backend modules in Module Inventory | Count rows in module table |
| Dependency tiers | Number of tiers in dependency graph | Count distinct tier levels |
| API surface | Total endpoint count across all modules | Count endpoint definitions in API spec |
| Module independence | Ratio of Tier 1 (zero-dependency) modules | Count Tier 1 modules / total modules |

### Decision Matrix

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ if module_count < 3:                                                        │
│   → Sequential                                                              │
│     Rationale: Too few modules. Parallel overhead exceeds benefit.          │
│     Action: No SubAgent forking. Backend then Frontend, in order.           │
│                                                                             │
│ if module_count is 3-4 AND endpoint_count > 15:                             │
│   → Scenario A Only (Frontend-Backend Parallel)                             │
│     Rationale: Enough API surface for frontend/backend split to pay off,    │
│     but too few modules for module-level parallelism to matter.             │
│     Action: Freeze API spec. Fork Backend + Frontend SubAgents.             │
│                                                                             │
│ if module_count >= 5 AND tier_1_modules >= 2:                               │
│   → Scenario A + B (Full Parallel)                                          │
│     Rationale: Enough independent modules for tier-based parallelism,        │
│     AND sufficient API surface for frontend-backend split.                   │
│     Action: Shared layer first, then tier-based backend modules,            │
│     with frontend SubAgent parallel to backend.                             │
│                                                                             │
│ if module_count >= 5 BUT all modules form a single dependency chain:        │
│   → Scenario A Only                                                         │
│     Rationale: Module-level parallelism yields no speedup when modules       │
│     must execute serially due to deep dependency chain.                      │
│     Action: Sequential backend modules. Frontend parallel with backend.     │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Strategy Output in `.spec/architecture.md`

The assessment result is recorded in the architecture spec under "Parallel Strategy
Recommendation" (Section 3). The Backend Engineer and Frontend Engineer read this
section to determine which SubAgent pattern to activate.

**Important**: The architect recommends, the user decides. Always present the assessment
with rationale and let the user approve or choose an alternative.

---

## When to Use Subagent

Use Subagent when ALL of these conditions are met:
1. The task involves reading many files or generating many files
2. The task does not require user interaction (no AskUserQuestion)
3. The output can be expressed as a structured summary (not full file contents)
4. The task has a clear, specifiable objective

Do NOT use Subagent for:
- User interaction (requirements gathering, design reviews)
- Small, focused tasks (single file edits, simple queries)
- Tasks where the main conversation needs full file contents to continue

## SubAgent Task Instruction Format

Every SubAgent task instruction must include:
1. **Source of truth** — Which files the SubAgent should read and follow
2. **Constraints** — What the SubAgent must NOT do
3. **Output format** — Exact structure of the expected return
4. **Deviation handling** — What to do when the spec seems wrong

```markdown
## Source of Truth
{list the files the SubAgent reads — typically frozen specs}

## Constraints
- {what the SubAgent must strictly follow}
- {what the SubAgent must NOT do}
- {how to handle unexpected situations}

## Output Format
{exact structure of the return, with field names and types}

## Deviation Handling
- If the spec seems incomplete: record in "Gaps" section, do not guess
- If the spec seems incorrect: record in "Deviations" section, do not modify
- If implementation requires unspecified decisions: record in "Assumptions" section
```

## SubAgent Return Format

All SubAgent returns should follow this structure:

```markdown
## Status
{COMPLETED | PARTIAL | BLOCKED}

## Summary
{1-3 sentence overview of what was accomplished}

## Details
{structured output specific to the scenario}

## Deviations
| Item | Spec Says | Actually Did | Reason |
|------|-----------|-------------|--------|

## Gaps
| Item | Description | Impact |
|------|-------------|--------|

## Files Written
| Path | Description |
|------|-------------|
```

---

## Scenario A: Backend + Frontend Parallel Development

**Stage**: 5 + 6 (after API spec is frozen)
**Parallelism**: Backend and Frontend SubAgents run simultaneously
**Prerequisite**: `.spec/api-spec.md` must be explicitly frozen and marked as non-modifiable

### Phase 1: Freeze Contract (main conversation)

Before forking, the main conversation confirms with the user:
1. `.spec/api-spec.md` is complete and approved
2. Mark the file as frozen: add `<!-- FROZEN: do not modify during parallel development -->` at the top
3. Both SubAgents will treat this as the immutable contract

### Phase 2: Fork Parallel SubAgents

**Backend SubAgent Task Instruction:**
```markdown
## Source of Truth
- `.spec/api-spec.md` (FROZEN — do not modify, follow exactly)
- `database/schema.sql` (table structures to query against)
- `backend/CLAUDE.md` (coding conventions, auto-loaded)

## Constraints
- Every API endpoint must match the FROZEN api-spec exactly
- Response JSON must match the spec's JSON Schema field by field
- Do NOT add endpoints not defined in the spec
- Do NOT rename fields defined in the spec
- Import shared entities from the shared layer (not redefine them)

## Task
1. Set up project scaffolding in backend/ (if not already done)
2. Implement all endpoints defined in .spec/api-spec.md
3. Connect to database using database/schema.sql structure
4. Implement authentication and error handling per spec
5. Write backend/README.md with setup and run instructions

## Output Format
Return a structured summary:
### Implemented Endpoints
| Method | Path | Status | Notes |
|--------|------|--------|-------|

### Deviations
| Endpoint | Spec Says | Actually Did | Reason |
|----------|-----------|-------------|--------|

### Gaps
| Endpoint | What's Missing | Impact |
|----------|---------------|--------|

### Files Written
| Path | Lines | Description |
|------|-------|-------------|

### README
{startup command and environment variables}
```

**Frontend SubAgent Task Instruction:**
```markdown
## Source of Truth
- `.spec/api-spec.md` (FROZEN — do not modify, follow exactly)
- `.spec/ui-design.md` (page layouts, design system)
- `frontend/CLAUDE.md` (coding conventions, auto-loaded)

## Constraints
- Every API call must use endpoints from the FROZEN api-spec
- Request/response handling must match the spec's JSON Schema
- Do NOT call endpoints not defined in the spec
- If UI design requires data not in the spec, record in Gaps, do not guess
- Follow the design system defined in .spec/ui-design.md

## Task
1. Set up project scaffolding in frontend/ (if not already done)
2. Implement all pages from .spec/ui-design.md
3. Integrate with API endpoints defined in .spec/api-spec.md
4. Implement responsive layout and state management
5. Write frontend/README.md with setup and run instructions

## Output Format
( same structure as Backend — Implemented Endpoints, Deviations, Gaps, Files Written, README )
```

### Phase 3: Contract Verification SubAgent

After both SubAgents complete, fork a verification SubAgent:

```markdown
## Task
Read .spec/api-spec.md, then scan all files in backend/ and frontend/.
Perform these checks:

1. Backend Response Check: For each endpoint, verify the backend's response
   JSON structure matches the spec's response schema (field names, types)
2. Frontend Request Check: For each API call in frontend code, verify it
   uses the correct endpoint, method, and request body from the spec
3. Frontend Response Parsing: Verify the frontend parses the same fields
   that the backend returns (cross-reference, not just spec check)
4. Error Handling: Verify both sides use the spec's error codes and formats

## Output Format
### Consistency Report
| Endpoint | Backend ✅/❌ | Frontend ✅/❌ | Cross ✅/❌ | Issue |
|----------|-------------|-------------|-----------|-------|

### Critical Issues (must fix before integration)
{issues that will cause runtime failures}

### Warnings (should fix but won't block)
{minor inconsistencies}

### Unchecked
{endpoints that could not be verified and why}
```

### Phase 4: Fix in Main Conversation

Based on the verification report:
- Critical issues → Fork a fix SubAgent or fix inline
- Spec gaps → Update `.spec/api-spec.md` and notify both sides
- User decisions needed → Discuss in main conversation
- Remove the FROZEN marker from api-spec.md when done

---

## Scenario B: Module-Level Parallel Development

**Stage**: 5 (Backend, when project has 5+ modules)
**Parallelism**: Modules within the same tier run simultaneously
**Prerequisite**: Architecture must define module dependency graph and internal interfaces

### Prerequisite: Architecture Deliverable Update

The architect's `.spec/architecture.md` must include:

```markdown
## Module Dependency Graph

Tier 1 (no dependencies):
  - user
  - product

Tier 2 (depends on Tier 1):
  - order → depends on: user, product
  - cart → depends on: user, product

Tier 3 (depends on Tier 2):
  - payment → depends on: order

## Module Internal Interfaces

### UserService (exposed to other modules)
- getUserById(userId: string): UserDTO
- validateUser(userId: string): boolean

### ProductService (exposed to other modules)
- getProductById(productId: string): ProductDTO
- checkStock(productId: string, qty: number): boolean
```

### Phase 1: Shared Layer SubAgent

```markdown
## Task
Read .spec/api-spec.md and database/schema.sql.
Generate the shared foundation code in backend/src/shared/:

1. Entity/Model classes — one per database table, fields match schema.sql exactly
   (table column names → camelCase, types map to language types)
2. Unified Response wrapper — { code, message, data } format per API spec
3. Global error handler — maps exceptions to spec error codes
4. Authentication middleware — JWT/session handling per architecture spec
5. Common utilities — pagination, date handling, validation helpers

## Constraints
- Entity field names must map 1:1 to database columns
- Entity field types must match database column types
- Use the exact response format from .spec/api-spec.md

## Output Format
### Generated Shared Classes
| Class | Source (table/spec) | Fields |
|-------|---------------------|--------|

### Type Mappings
| DB Column | DB Type | Language Type | Notes |
|-----------|---------|--------------|-------|

### Files Written
{list}
```

### Phase 2: Tier-Based Module SubAgents

For each tier, fork parallel SubAgents (one per module in the tier):

```markdown
## Task
Implement the {module_name} module in backend/src/{module_name}/.

## Source of Truth
- `.spec/api-spec.md` — endpoints for this module
- `backend/src/shared/` — shared entities and utilities (ALREADY GENERATED, import these)
- `.spec/architecture.md` Module Internal Interfaces — service methods other modules will call

## Constraints
- Import entities from shared layer, do NOT redefine them
- Expose the internal interface methods defined in architecture.md
- Follow the controller → service → repository pattern
- All response formats must use the shared Response wrapper

## For Tier 2+ modules:
- Import services from dependency modules via their defined internal interfaces
- Do NOT directly access dependency module's repository or database tables

## Output Format
( same as Scenario A: Implemented Endpoints, Deviations, Gaps, Files Written )
Plus:
### Internal Interfaces Exposed
| Method | Signature | Description |
|--------|-----------|-------------|

### Dependencies Used
| Module | Methods Called |
|--------|---------------|
```

### Phase 3: Integration Verification SubAgent

```markdown
## Task
Scan all backend source code and verify:

1. Entity Consistency: All Entity classes match database/schema.sql column names and types
2. Cross-Module Calls: Modules only call other modules through defined internal interfaces
3. No Circular Dependencies: No module A imports module B which imports module A
4. Response Uniformity: All endpoints use the shared Response wrapper
5. API Spec Coverage: Every spec endpoint is implemented somewhere

## Output Format
### Consistency Report
{same format as Scenario A Phase 3}
### Dependency Graph (actual)
{what was found vs what was specified}
### Circular Dependencies
{none found, or list them}
```

---

## Scenario C: Test Scope Aggregation

**Stage**: 7 (Test Engineer)
**Purpose**: Read all spec files and produce a concise test scope summary
**Single SubAgent, not parallel**

```markdown
## Task
Read ALL files in .spec/ directory and .spec/handoffs/ directory.
Produce a test scope summary covering:

1. Feature Modules: List each feature module, its API endpoints, and key user stories
2. Data Constraints: Database constraints that affect test data (foreign keys, unique checks, etc.)
3. Critical Flows: End-to-end user journeys that span multiple features
4. Risk Areas: Features with complex logic, external dependencies, or known issues from handoff notes

## Constraints
- Do NOT include implementation details — only what to test, not how to test
- Keep under 100 lines
- Focus on scope, not test cases (test cases come later)

## Output Format
### Feature Modules
| Module | Endpoints | Key Stories | Risk Level |
|--------|-----------|-------------|-----------|

### Data Constraints
{constraints that affect test data setup}

### Critical E2E Flows
{ordered list of multi-step user journeys}

### Risk Areas
{features requiring extra testing attention}
```

---

## Scenario D: Security Code Scanning

**Stage**: 7 (Security Engineer)
**Two parallel SubAgents: backend scan + frontend scan**

**Backend Security SubAgent:**
```markdown
## Task
Scan ALL files in backend/ directory for security issues.
Check against OWASP Top 10:

1. Injection (SQL, command) — search for raw queries, string concatenation in SQL
2. Broken Authentication — check auth middleware, token handling, session management
3. Sensitive Data Exposure — check for plaintext passwords, PII in logs, missing encryption
4. Access Control — verify role checks on all non-public endpoints
5. Security Misconfiguration — check CORS, error message detail, debug endpoints
6. Input Validation — verify all API inputs are validated before use

## Output Format
### Findings
| Severity | Category | File | Line | Description | Fix |
|----------|----------|------|------|-------------|-----|

### Security Checklist
| Check | Status | Notes |
|-------|--------|-------|

### Recommendations
{ordered by priority}
```

**Frontend Security SubAgent:**
```markdown
## Task
Scan ALL files in frontend/ directory for security issues:

1. XSS — check for dangerouslySetInnerHTML, unescaped user input in DOM
2. CSRF — verify CSRF token handling in forms and API calls
3. Sensitive Data Storage — check localStorage/sessionStorage for tokens, PII
4. API Security — verify all API calls use HTTPS, proper auth headers
5. Third-party Dependencies — flag any known vulnerable packages

## Output Format
(same as Backend Security SubAgent)
```

---

## Scenario E: Deployment Config Generation

**Stage**: 8 + 10 (DevOps Engineer — Staging and Production)
**Single SubAgent for mechanical config generation**

```markdown
## Task
Read .spec/architecture.md (deployment architecture section),
backend/README.md (runtime requirements), and frontend/README.md (build requirements).
Generate the following files in devops/:

1. Dockerfile — multi-stage build (build stage + runtime stage)
2. docker-compose.yml — all services, networks, volumes, health checks
3. ci.yml — CI/CD pipeline for the appropriate platform

## Constraints
- Use minimal base images (alpine/distroless)
- Run as non-root user
- Include health checks
- Do NOT hardcode secrets — use environment variables
- Match the tech stack defined in CLAUDE.md

## Output Format
### Files Written
| Path | Description | Key Decisions |
|------|-------------|---------------|

### Environment Variables Required
| Variable | Purpose | Default |
|----------|---------|---------|

### Ports
| Service | Port | Protocol |
|---------|------|----------|

### Known Limitations
{anything the config doesn't cover yet}
```

---

## Scenario F: Bug Fix Coordination

**Stage**: 7 (Test Engineer — bug fix phase)
**Purpose**: Coordinate bug resolution through SubAgents without leaving the Test Engineer role
**Parallelism**: Pre-check SubAgents run in parallel; Fix SubAgents run in parallel per responsibility group

### Overview

After test execution and security audit produce the bug list, the Test Engineer acts as coordinator.
SubAgents handle clarification, assessment, and fixes. The Test Engineer stays as the active role
throughout — no role switching required.

### SubAgent Types

| SubAgent | Trigger | Purpose | Count |
|----------|---------|---------|-------|
| Product Manager Clarification | Bug classified as "Requirement Ambiguity" | Determine if it's a bug or requirement gap | Per ambiguous bug |
| Architect Assessment | Bug classified as "Architecture Impact" or SEC-High/Critical | Provide fix direction + architecture constraints | Per architecture-impacting bug |
| Backend Fix | Backend-responsibility bugs at Critical/High | Fix backend code | 1 (batched) |
| Frontend Fix | Frontend-responsibility bugs at Critical/High | Fix frontend code | 1 (batched) |
| Security Fix | Security bugs at Critical/High | Fix security vulnerabilities | 1 (batched) |
| Project Manager Assessment | Blocking bugs > 5 or fix time > 20% of cycle | Evaluate progress risk | 0-1 |

### Workflow Phases

#### Phase 1: Bug Triage

Test Engineer classifies each bug on three axes:

```
Severity:     Critical / High / Medium / Low / Info
Responsibility: Backend / Frontend / Cross-stack / Unclear
Decision Type:  Pure Technical / Requirement Ambiguity / Architecture Impact
```

Severity gate determines what happens next:
- **Critical/High** → Must be resolved before Stage 8 (Staging). Enter fix workflow.
- **Medium/Low/Info** → Record to Backlog. Do not block deployment.

#### Phase 2: Pre-check SubAgents (conditional, parallel)

Fork pre-check SubAgents for bugs that need clarification before fixing.

**Product Manager Clarification SubAgent:**
```markdown
你是产品经理。有一个 Bug 需要你判断性质。

## Bug 描述
- BUG-{id}: {title}
- 现象：{what happens}
- 预期：{what should happen}

## 判断依据
请阅读 .spec/prd.md 中与该功能相关的用户故事和验收标准，判断：
1. 这是 Bug（PRD 有明确要求，但实现不符合）？
2. 还是需求遗漏（PRD 没有覆盖这个场景）？

## 产出格式
### 结论
{Bug / 需求遗漏 / 需要进一步讨论}

### 依据
{引用 PRD 中的具体内容}

### 建议
{如果是 Bug：修复方向；如果是需求遗漏：是否纳入当前迭代}
```

**Architect Assessment SubAgent:**
```markdown
你是技术架构师。有一个 Bug 涉及架构层面，需要你评估修复方向。

## Bug 描述
- BUG-{id}: {title}
- 现象：{what happens}
- 涉及组件：{affected components}

## 评估要求
请阅读 .spec/architecture.md 和相关代码，提供：
1. 这个 Bug 是否影响架构设计的假设？
2. 修复方向建议（不要写代码，给方向）
3. 修复时必须遵守的架构约束

## 产出格式
### 架构影响评估
{是否影响架构假设}

### 修复方向
{建议的修复策略}

### 约束条件
{修复时必须遵守的规则，后端工程师 SubAgent 需要遵循这些约束}
```

#### Phase 3: Fix SubAgents (parallel by responsibility group)

Group all blocking bugs by responsibility and fork fix SubAgents in parallel.

**Backend Fix SubAgent:**
```markdown
你是后端工程师，负责修复以下 Bug。

## Bug 列表
{List each bug with: ID, title, severity, location, expected behavior}

## 必读文件
1. .spec/api-spec.md — 相关 Section 的 API 契约
2. database/schema.sql — 数据库结构（如涉及）
3. {affected backend files} — 当前实现

{If architect pre-check was done:}
## 架构约束（必须遵守）
{Architect's constraints from Phase 2}

## 修复要求
- 只修改与 Bug 相关的代码，不做额外重构
- 每个 Bug 的修复要独立可识别（方便回归验证）
- 修复后确认不影响其他端点
- 如发现修复需要更广泛的改动，记录在 Assumptions 中，不要自行扩大范围

## 产出格式
### Status
{COMPLETED | PARTIAL | BLOCKED}

### 修复内容
| Bug ID | 文件 | 修改说明 |
|--------|------|----------|

### 影响范围分析
{可能受影响的其他功能}

### Deviations
| Bug ID | 预期修复 | 实际修复 | 原因 |
|--------|---------|---------|------|

### Assumptions
{修复过程中做出的假设或需要进一步确认的事项}

### Files Modified
| Path | Change Summary |
|------|---------------|
```

**Frontend Fix SubAgent:**
```markdown
你是前端工程师，负责修复以下 Bug。

## Bug 列表
{List each bug with: ID, title, severity, location, expected behavior}

## 必读文件
1. .spec/ui-design.md — 相关页面的设计规范
2. .spec/api-spec.md — API 契约（如涉及 API 对接）
3. {affected frontend files} — 当前实现

{If PM clarification was done:}
## 需求澄清
{PM's clarification from Phase 2}

## 修复要求
- 只修改与 Bug 相关的代码，不做额外重构
- 保持与设计规范一致
- 如修复涉及交互行为变化，记录在 Deviations 中

## 产出格式
{Same as Backend Fix SubAgent}
```

**Security Fix SubAgent:**
```markdown
你是安全工程师，负责修复以下安全漏洞。

## Bug 列表
{List each SEC bug with: ID, title, severity, risk description, affected files}

## 必读文件
1. docs/security-audit.md — 相关安全发现
{If architect pre-check was done:}
2. 架构师评估 — 修复方向和约束
3. {affected files} — 当前实现

## 修复要求
- 修复方案必须符合架构师给定的约束条件
- 不引入新的安全风险
- 每个 Bug 的修复要独立可识别
- 修复后简要说明为什么新方案是安全的

## 产出格式
{Same as Backend Fix SubAgent, plus:}

### Security Justification
| Bug ID | 修复方案 | 安全性论证 |
|--------|---------|-----------|
```

#### Phase 4: Progress Risk Assessment (conditional)

Only fork when: blocking bug count > 5, or estimated fix time > 20% of cycle duration.

```markdown
你是项目经理，需要评估当前 Bug 修复对项目进度的影响。

## 当前情况
- 阻塞级 Bug 数量：{count}
- Bug 摘要：{table of blocking bugs}
- 当前周期：{cycle name and timeline}
- 已用时间占比：{percentage}

## 评估要求
1. 风险评估：修复这些 Bug 是否会导致周期延误？
2. 建议方案：继续修复 / 缩减范围 / 调整计划
3. 如果建议缩减范围，哪些 Bug 可以降级？

## 产出格式
### 风险等级
{High / Medium / Low}

### 建议
{具体方案和理由}

### 范围调整建议（如适用）
| Bug ID | 原严重度 | 建议降级为 | 理由 |
|--------|---------|-----------|------|

### Timeline Impact
{预计修复时间，对整体进度的影响}
```

The user has final decision on whether to accept the PM's recommendation.

#### Phase 5: Review and Merge (main conversation)

When fix SubAgents complete:
1. Test Engineer reviews each SubAgent's output in the main conversation
2. Verify changes are minimal and focused
3. Check for unexpected Deviations or Assumptions
4. Merge changes into the working directory
5. Proceed to regression verification

#### Phase 6: Regression Verification

Test Engineer re-executes test cases:
- Test cases directly related to fixed bugs → must pass
- Test cases for potentially affected features → should pass
- If regression reveals new bugs → classify and add to bug list
- If a fix failed: can re-fork one more fix round (max 2 rounds total per bug)
- After 2 failed fix rounds: recommend exception mode for manual resolution

#### Phase 7: Resolution Summary and Exit

```markdown
## Bug Resolution Summary

### Fixed
| Bug ID | Severity | Fix Agent | Verification |
|--------|----------|-----------|-------------|
| BUG-001 | High | Backend SubAgent | ✅ Pass |

### Deferred (not blocking deployment)
| Bug ID | Severity | Description | Plan |
|--------|----------|-------------|------|
| BUG-002 | Medium | Frontend pagination scroll | Next iteration |

### Pre-deployment Checklist
- [ ] All Critical/High bugs fixed and verified
- [ ] Deferred bugs acknowledged by user
- [ ] No regressions introduced
```

Write the resolution summary into the handoff note. The DevOps engineer needs to know
about any configuration changes from security fixes.

### Decision Flow Diagram

```
Test Execution Complete
        │
        ▼
   Bug Triage (triple classification)
        │
        ├── Critical/High ──→ Enter fix workflow
        │       │
        │       ├── Requirement Ambiguity? ──→ Fork PM Clarification SubAgent
        │       ├── Architecture Impact? ────→ Fork Architect Assessment SubAgent
        │       │
        │       ▼
        │   Fork Fix SubAgents (parallel by responsibility)
        │       │
        │       ├── Blocking bugs > 5? ──→ Fork PM Risk Assessment SubAgent
        │       │
        │       ▼
        │   Review + Merge fixes
        │       │
        │       ▼
        │   Regression Verification
        │       │
        │       ├── Pass ──→ Resolution Summary → Write Handoff → Stage 8 (Staging)
        │       └── Fail ──→ Re-fork (max 1 more round) ──→ If still fails → Exception mode
        │
        └── Medium/Low/Info ──→ Backlog ──→ Include in handoff note
```

---

## Scenario G: UAT Bug Fix Coordination (Lightweight)

**Stage**: 9 (Product Manager — UAT bug fix phase)
**Purpose**: Coordinate Critical/High bug fixes found during user trial
**Compared to Scenario F**: No pre-check SubAgents, no progress risk assessment,
max 1 regression round, adds user re-verification step

### SubAgent Types

| SubAgent | Trigger | Purpose | Count |
|----------|---------|---------|-------|
| Backend Fix | Backend-responsibility Critical/High bug | Fix backend code | 1 (batched) |
| Frontend Fix | Frontend-responsibility Critical/High bug | Fix frontend code | 1 (batched) |

No PM Clarification SubAgent — requirements already validated in Stage 7.
No Architect Assessment SubAgent — architecture already validated in Stage 7.
No Progress Risk Assessment SubAgent — UAT bugs typically few (< 5).

### SubAgent Task Instructions

**Backend Fix SubAgent:**
```markdown
你是后端工程师，负责修复 UAT 阶段发现的 Bug。

## Bug 列表
{List each bug with: FB-{id}, title, severity, description, what the user expected}

## 必读文件
1. .spec/api-spec.md — 相关 API 契约
2. database/schema.sql — 数据库结构（如涉及）
3. {affected backend files} — 当前实现
4. .spec/handoffs/stage7_testing.md — 已修复的 Bug 清单（避免重复修复）

## 修复要求
- 只修改与 Bug 相关的代码，不做额外重构
- 这个 Bug 在 Stage 7 测试时未被发现，说明是边缘场景——修复时考虑完整场景覆盖
- 修复后确认不影响其他端点
- 如发现修复需要更广泛的改动，记录在 Assumptions 中，不要自行扩大范围

## 产出格式
### Status
{COMPLETED | PARTIAL | BLOCKED}

### 修复内容
| FB ID | 文件 | 修改说明 |
|-------|------|----------|

### 影响范围分析
{可能受影响的其他功能}

### Files Modified
| Path | Change Summary |
|------|---------------|
```

**Frontend Fix SubAgent:**
```markdown
你是前端工程师，负责修复 UAT 阶段发现的 Bug。

## Bug 列表
{List each bug with: FB-{id}, title, severity, description, what the user expected}

## 必读文件
1. .spec/ui-design.md — 相关页面设计规范
2. .spec/api-spec.md — API 契约（如涉及 API 对接）
3. {affected frontend files} — 当前实现
4. .spec/handoffs/stage7_testing.md — 已修复的 Bug 清单（避免重复修复）

## 修复要求
- 只修改与 Bug 相关的代码，不做额外重构
- 保持与设计规范一致
- 考虑用户描述的实际使用场景，不只是复现 Bug

## 产出格式
{Same as Backend Fix SubAgent}
```

### Workflow

```
User reports Bug during UAT
        │
        ▼
PM classifies: Bug / UX Issue / Feature Request
        │
        ├── Feature Request → Next Version Input
        ├── UX Issue → Quick fix or Next Version Input
        │
        └── Bug → Classify severity
                │
                ├── Medium/Low → Backlog (Next Version Input)
                │
                └── Critical/High → PM classifies responsibility
                        │
                        ▼
                Fork Fix SubAgent(s) by responsibility
                (Backend / Frontend, parallel if cross-stack)
                        │
                        ▼
                PM reviews fix result
                        │
                        ▼
                User re-verifies
                        │
                        ├── Pass → Resume UAT
                        │
                        └── Fail → 1 more fix round
                                │
                                ├── Pass → Resume UAT
                                │
                                └── Fail → Defer to Backlog
                                           or trigger exception switch
```

### Key Differences from Scenario F (Stage 7)

| Aspect | Scenario F (Stage 7) | Scenario G (Stage 9) |
|--------|---------------------|---------------------|
| Coordinator | Test Engineer | Product Manager |
| Pre-check SubAgents | PM Clarification + Architect Assessment | None |
| Risk Assessment SubAgent | Conditional (blocking > 5) | None |
| Max regression rounds | 2 per bug | 1 per bug |
| User verification | None (internal testing) | Required after each fix |
| Bug ID prefix | BUG-{N} | FB-{N} |
| Bug volume expectation | Potentially many | Typically few (< 5) |
