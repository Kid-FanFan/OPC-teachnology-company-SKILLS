# Version Transition Reference

This file defines how the project transitions from one version/cycle to the next.
It covers document archiving, baseline spec evolution, database migration, and
the streamlined initialization for subsequent versions.

## Core Principle

Cross-version transitions follow the same progressive loading principle as
within-version work. Each role in v2 only reads the v1 artifacts it needs —
never everything at once.

## Document Classification

After a version is deployed (Stage 10 complete), all documents fall into three
categories:

### Baseline Specs (kept and evolved)

These files remain in `.spec/` and are updated in-place for the next version:

| File | Evolution Strategy |
|------|-------------------|
| `.spec/prd.md` | Keep v1 scope as baseline, add v2 scope in new section |
| `.spec/api-spec.md` | Keep v1 endpoints, add v2 endpoints under "v2 Additions" |
| `.spec/architecture.md` | Keep v1 modules, add v2 modules and update diagrams |
| `.spec/database.md` | Keep v1 tables, add v2 tables/changes |
| `.spec/ui-design.md` | Keep v1 design system + pages, add v2 pages |

**Version markers** — Every baseline spec file gets two header comments:

```markdown
<!-- VERSION: v1 baseline -->
<!-- LAST_UPDATED: v2 -->
```

When a role reads a baseline spec, the Role Context Guide tells them which
sections are v2-relevant and which are v1 baseline (readable via handoff summary).

### Project-Persistent Docs (kept as-is)

| File | Reason |
|------|--------|
| `CLAUDE.md` | Tech stack and coding conventions persist across versions |
| `docs/project-charter.md` | Long-term vision, scale expectations |
| `.claude/rules/*` | Path-scoped rules still apply |
| `{dir}/CLAUDE.md` | Subdirectory conventions still apply |

`CLAUDE.md` may need minor updates if v2 introduces new frameworks or tools.

### Version Snapshots (archived)

These files are specific to the completed version and moved to `.archive/v{N}/`:

```
.spec/handoffs/*          → .archive/v{N}/handoffs/
.spec/test-cases.md       → .archive/v{N}/test-cases.md
docs/test-report.md       → .archive/v{N}/docs/
docs/security-audit.md    → .archive/v{N}/docs/
docs/uat-report.md        → .archive/v{N}/docs/
docs/release-notes.md     → .archive/v{N}/docs/
docs/user-guide.md        → .archive/v{N}/docs/
docs/deployment-guide.md  → .archive/v{N}/docs/
.doc/html_report/*.html   → .archive/v{N}/html_report/
```

Archived files are available for reference but not loaded automatically.
Roles access them only when explicitly needed (e.g., Test Engineer reading v1
test cases for regression testing).

## Archive Operation

Performed after Stage 10 (Production Deployment) completes, before v2 Stage 0 begins.

### Step 1: Create Archive Directory

```
mkdir -p .archive/v{N}/docs
mkdir -p .archive/v{N}/handoffs
mkdir -p .archive/v{N}/html_report
```

### Step 2: Move Version Snapshots

```bash
# Handoff notes
mv .spec/handoffs/stage*.md .archive/v{N}/handoffs/

# Test cases
mv .spec/test-cases.md .archive/v{N}/test-cases.md

# Human docs (version-specific)
mv docs/test-report.md .archive/v{N}/docs/
mv docs/security-audit.md .archive/v{N}/docs/
mv docs/uat-report.md .archive/v{N}/docs/
mv docs/release-notes.md .archive/v{N}/docs/
mv docs/user-guide.md .archive/v{N}/docs/
mv docs/deployment-guide.md .archive/v{N}/docs/

# HTML reports (regenerated each version)
mv .doc/html_report/*.html .archive/v{N}/html_report/
```

### Step 3: Clean Up for Next Version

```bash
# Clear handoffs directory (v2 starts fresh)
# .spec/handoffs/ is now empty

# Remove v1 next-version-input (will be regenerated at v2 UAT)
rm .spec/next-version-input.md
```

### Step 4: Update Baseline Specs

Add version markers to each baseline spec file:

```markdown
<!-- VERSION: v1 baseline -->
<!-- LAST_UPDATED: v2 -->
```

### Step 5: Reset PROJECT_STATE.md

Update PROJECT_STATE.md for the new cycle:
- Add v{N} to Version History (status: completed, archive path)
- Add v{N+1} to Version History (status: in_progress)
- Update Cycle Plan with new cycle
- Reset Stage Status table (all pending except Stage 0 in_progress)
- Reset Role Context Guide for v2 Stage 0
- Keep Environment Capabilities (re-detect in Stage 0)
- Keep Key Decisions Log (append new decisions)
- Reset Deliverables Index for v2

### Step 5b: Baseline Spec Compression Check

After resetting PROJECT_STATE.md, check each `.spec/*.md` baseline file for compression:
1. Count version additions (## v{N} Additions headers) and total lines
2. If additions ≥ 3 OR lines > 800 → compress that file
3. Compression: save full file to `.archive/v{N}/specs/`, then rewrite with summary
4. See "Version Compression" section below for details and examples

### PROJECT_STATE.md Reset Template

下面是 PM 重置 PROJECT_STATE.md 的具体模板。**不是创建空白文档**，而是保留累积知识（决策、风险、环境能力），重置流程状态（阶段、交付物、上下文指南）。

```markdown
# Project State: {project_name}

## Project Info
- **Name**: {project_name}
- **Dev Model**: {model}
- **Tech Stack**: {tech stack — 保持不变，如 v2 有变化在 Stage 0 R2 更新}
- **Created**: {original creation date}
- **Last Updated**: {today}

## Version History
- **v1 ({cycle_name})**: completed
  - Scope: {v1 scope summary, one line}
  - Completed: {date}
  - Archive: .archive/v1/
- **v2 ({cycle_name})**: in_progress
  - Scope: {from next-version-input.md summary}

## Current Status
- **Current Cycle**: {v2 cycle name}
- **Current Stage**: 0 - Initialization (v2 streamlined)
- **Current Role**: Project Manager
- **Overall Progress**: 0%

## Cycle Plan

### Cycle: {v1 name}
- **Scope**: {v1 scope}
- **Status**: completed

### Cycle: {v2 name}
- **Scope**: {from next-version-input.md Section 6}
- **Status**: in_progress

{planned cycles remain as-is}

## Stage Status

| Stage | Role | Status | Deliverables |
|-------|------|--------|-------------|
| 0. Init | Project Manager | in_progress | — |
| 1. Requirements | Product Manager | pending | — |
| 2. UI Design | UI Designer | pending | — |
| 3. Architecture | Tech Architect | pending | — |
| 4. Database | DB Engineer | pending | — |
| 5. Backend | Backend Engineer | pending | — |
| 6. Frontend | Frontend Engineer | pending | — |
| 7. Testing | Test + Security Engineer | pending | — |
| 8. Staging | DevOps Engineer | pending | — |
| 9. UAT | Product Manager | pending | — |
| 10. Production | DevOps Engineer | pending | — |

## Role Switch Log

{清空 v1 日志，从空开始。v1 的完整日志已归档在 .archive/v1/PROJECT_STATE.log}

## Environment Capabilities
{保留 v1 检测结果。Stage 0 Round 3 会重新检测并更新。}

## Key Decisions Log
{保留 v1 全部决策，这些是累积知识，v2 必须遵守。}

| Stage | Decision | Choice | Rationale |
|-------|----------|--------|-----------|
{... v1 decisions ...}

{v2 新增决策追加在下方}

## Risk Register
{保留 v1 风险 + 新增 v2 风险}

## Deliverables Index

| Stage | File Path | Description | Version | Status |
|-------|-----------|-------------|---------|--------|
| 0 | CLAUDE.md | AI project instructions | baseline | ✅ |
| 0 | docs/project-charter.md | Project charter | baseline | ✅ |
| 0 | backend/CLAUDE.md | Backend conventions | baseline | ✅ |
| 0 | frontend/CLAUDE.md | Frontend conventions | baseline | ✅ |
| 0 | database/CLAUDE.md | Database conventions | baseline | ✅ |
| 0 | devops/CLAUDE.md | DevOps conventions | baseline | ✅ |
| 0 | .claude/rules/* | Path-scoped rules | baseline | ✅ |
| 1 | .spec/prd.md | PRD (baseline + v2 additions) | evolved | 📝 |
| 2 | .spec/ui-design.md | UI Design (baseline + v2) | evolved | 📝 |
| 3 | .spec/architecture.md | Architecture (baseline + v2) | evolved | 📝 |
| 3 | .spec/api-spec.md | API Spec (baseline + v2) | evolved | 📝 |
| 4 | .spec/database.md | DB Design (baseline + v2) | evolved | 📝 |
| 4 | database/schema.sql | DDL (always latest) | baseline | ✅ |
| 4 | database/seed_data.sql | Seed data | baseline | ✅ |
| 4 | database/data_dict.md | Data dictionary | baseline | ✅ |
| 5+ | {to be filled by v2 stages} | — | — | pending |

备注：标记 baseline = v1 已存在，evolved = 需要追加 v2 内容，📝 = 待更新

## Role Context Guide

### Current Role: Project Manager — Stage 0: Initialization (v2 streamlined)

**Must Read**:
1. `.spec/next-version-input.md` — v2 范围输入（五源汇总）
2. `docs/project-charter.md` — 长期愿景和规模预期

**Read in Stage 0 Round 2**:
3. `.spec/architecture.md` — 评估技术栈变化（新增 MinIO/LangChain 等）

**Baseline Reference** (read when needed):
4. `.spec/prd.md` — v1 基线需求（Stage 1 时读取 v2 Additions 部分）

**Archive** (only when explicitly needed):
5. `.archive/v1/docs/test-cases.md` — Stage 7 回归测试时参考

**Skip**:
- `.archive/v1/handoffs/*` — v1 交接信息，v2 不需要
```

### Section-by-Section 处理规则

| Section | 操作 | 理由 |
|---------|------|------|
| Project Info | 保留，更新 Last Updated | 项目基本属性不变 |
| Version History | **新增** | v1 标记 completed + archive path，v2 标记 in_progress |
| Current Status | **重置** | 指向 v2 Stage 0 |
| Cycle Plan | **更新** | v1 标记 completed，v2 标记 in_progress |
| Stage Status | **重置** | 全部 pending，Stage 0 标记 in_progress |
| Role Switch Log | **清空** | v2 从空开始，v1 日志可归档 |
| Environment Capabilities | **保留** | Stage 0 Round 3 重新检测后更新 |
| Key Decisions Log | **保留** | 累积知识，v2 必须遵守 v1 的决策 |
| Risk Register | **保留** | v1 风险仍然存在，v2 新增追加 |
| Deliverables Index | **部分重置** | baseline 文件标记保留，v1 临时文件移除，v2 交付物随阶段追加 |
| Role Context Guide | **更新** | 指向 v2 Stage 0 PM 的必读文件 |

## Baseline Spec Evolution Pattern

When a role updates a baseline spec in v2, follow this pattern:

### Adding new content (most common)

```markdown
# api-spec.md

<!-- VERSION: v1 baseline -->
<!-- LAST_UPDATED: v2 -->

... v1 content (unchanged) ...

---

## v2 Additions

### {New Endpoint Group}

#### GET /api/v2/new-endpoint
**Purpose**: {what this does}
**Auth**: {role}
**Request**: ...
**Response**: ...
```

### Modifying existing content

When v2 changes a v1 endpoint/table/module:

```markdown
<!-- MODIFIED_IN: v2 -->
{modified content}

<!-- V1_ORIGINAL (archived in .archive/v1/) -->
{original v1 content, commented out or summarized}
```

Do NOT delete v1 content entirely — keep it as reference for roles that need
to understand the evolution. But use comments/markers to keep it unobtrusive.

### Deleting content (rare)

If v2 removes a feature entirely:

```markdown
<!-- REMOVED_IN: v2 — was: {brief description of what was removed} -->
<!-- See .archive/v1/ for full v1 spec -->
```

### Version Compression (when baseline specs grow too large)

As versions accumulate, baseline specs grow continuously. Without compression, a file that
started at 400 lines in v1 could reach 1500+ lines by v4, causing context explosion for
roles that need to read it.

**Trigger condition** — During version archive (after Stage 10), PM checks each baseline
spec against these thresholds:

| Condition | Threshold | Rationale |
|-----------|-----------|-----------|
| Version additions count | ≥ 3 versions (e.g., v1 baseline + v2 + v3 + v4) | After 3 layers, reading cost exceeds value |
| Total file lines | > 800 lines | Single file exceeding this risks context overflow |

If **either** condition is met, compress that file.

**Compression rules**:
1. **Latest version's additions** → Keep in full (v{N} Additions section stays unchanged)
2. **All older versions** → Replace with a condensed summary paragraph
3. **Original full content** → Save to `.archive/v{N}/specs/{filename}` before compressing
4. **One file per compression** — Only the version just completed triggers compression,
   so at most one version's content gets summarized at a time

**Before compression** (v4 archive, api-spec.md has 1200 lines):
```markdown
<!-- VERSION: v1 baseline -->
<!-- LAST_UPDATED: v4 -->

... v1 baseline content (400 lines) ...

---

## v2 Additions
... v2 endpoints (200 lines) ...

---

## v3 Additions
... v3 endpoints (300 lines) ...

---

## v4 Additions
... v4 endpoints (300 lines) ...
```

**After compression** (same file, now ~500 lines):
```markdown
<!-- VERSION: v1~v3 compressed -->
<!-- LAST_UPDATED: v4 -->
<!-- FULL_HISTORY: .archive/v4/specs/api-spec.md -->

... v1 baseline content (kept, this is the foundation) ...

---

## v2~v3 Summary

v2 新增了知识库管理（CRUD）、文档上传/解析（MinIO + LangChain）、
向量检索（pgvector）相关端点，共 12 个端点。详见 .archive/v4/specs/api-spec.md。

v3 新增了用户管理后台（用户列表、角色分配、启用/禁用）、系统配置、
操作日志相关端点，共 8 个端点。详见 .archive/v4/specs/api-spec.md。

---

## v4 Additions

#### GET /api/v4/new-endpoint
**Purpose**: {what this does}
...
```

**Summary writing guidelines**:
- Each compressed version gets 2-3 lines: what modules/features were added, how many endpoints/tables/pages
- Include the count so roles can assess scope without reading details
- Point to `.archive/` for the full content when a role needs specifics

**What gets archived**:
```
.archive/v{N}/
├── specs/                           ← NEW: compressed spec snapshots
│   ├── api-spec.md                  ← full version BEFORE compression
│   ├── architecture.md              ← (only files that were actually compressed)
│   └── ...
├── handoffs/
└── docs/
```

**What Project Manager does during archive**:

```
Step 5 (Reset PROJECT_STATE) 之后，增加 Step 5b:

Step 5b: Baseline Spec Compression Check

For each file in .spec/*.md:
  1. Count version additions (## v{N} Additions headers)
  2. Count total lines
  3. If additions ≥ 3 OR lines > 800:
     a. Copy full file to .archive/v{N}/specs/{filename}
     b. Rewrite file: keep baseline + latest additions in full,
        replace older additions with summary paragraphs
     c. Add FULL_HISTORY marker pointing to archive
  4. Update Role Context Guide to reflect compressed structure
```

**Impact on Role Context Guide**:

After compression, the guide distinguishes between compressed and current content:

```
### Current Role: {role} — Stage {N}: {stage name} (v4)

**Must Read** (current version):
1. `.spec/api-spec.md` Section "v4 Additions" — {what's new}

**Baseline Summary** (in-file, enough for context):
2. `.spec/api-spec.md` Section "v2~v3 Summary" — condensed overview

**Baseline Original** (always available, read when needed):
3. `.spec/api-spec.md` baseline content — v1 foundation

**Archive** (only when explicitly needed):
4. `.archive/v4/specs/api-spec.md` — full v2+v3 details for deep reference

**Skip**:
- `.archive/v1/specs/*` — already compressed into summary, no need to read
```

## Database Migration Strategy

v2 may need to modify v1's database structure. Add a `migrations/` directory:

```
database/
├── schema.sql              # Always the latest complete DDL (v1 + v2 all tables)
├── migrations/
│   ├── v2_001_{descriptive_name}.sql   # Incremental ALTER/CREATE
│   └── v2_002_{descriptive_name}.sql
├── seed_data.sql           # Updated seed data
└── data_dict.md            # Updated data dictionary
```

### Rules

1. `schema.sql` is always a **complete, fresh-install-ready** DDL. It represents
   the current state of the database (all versions combined).

2. `migrations/v{N}_*.sql` files are **incremental changes** that transform
   the previous version's schema to the current version. They must be idempotent
   (safe to run multiple times).

3. Migration files are numbered: `v{N}_{sequence}_{description}.sql`

4. The DB Engineer updates both `schema.sql` (full snapshot) AND creates
   migration files (incremental) for each schema change.

5. DevOps Engineer uses:
   - Fresh install: `schema.sql` + `seed_data.sql`
   - Upgrade from v{N-1}: `migrations/v{N}_*.sql`

### Migration File Template

```sql
-- Migration: v2_001_add_comments_table
-- Version: v1 → v2
-- Description: Add comments table for user feedback feature

-- Idempotent: check if table exists before creating
CREATE TABLE IF NOT EXISTS comments (
    id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL REFERENCES users(id),
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Add index for performance
CREATE INDEX IF NOT EXISTS idx_comments_user_id ON comments(user_id);
CREATE INDEX IF NOT EXISTS idx_comments_created_at ON comments(created_at);
```

## v2+ Streamlined Initialization

When starting v2 or later, Stage 0 is shorter than v1's 6-round process.

### v1 Stage 0 (full initialization)

6 rounds: vision, scope, tech, model, constraints, env detection.

### v2+ Stage 0 (streamlined)

3 rounds:

**Round 1: v{N} Scope Confirmation**
- Project Manager reads `.spec/next-version-input.md` (from previous version's UAT)
- Presents the synthesized scope to the user
- User confirms, adjusts, or adds new items
- PM updates the scope in PROJECT_STATE.md

**Round 2: Technical Changes**
- Any tech stack changes for this version?
- Any new external dependencies or integrations?
- Any adjustments to the development model?
- If nothing changed: skip this round

**Round 3: Environment Re-detection**
- Fork Environment Detection SubAgent (same as v1 Stage 0)
- Compare results with previous detection in PROJECT_STATE.md
- Note any new tools available or tools no longer available

### v2+ Stage 0 Entry Greeting

```
你好，我是项目经理。欢迎开始 v{N} 的开发。

v{N-1} 已经成功部署交付。根据上次 UAT 的反馈和 PRD 中延迟的需求，
我整理了 v{N} 的建议范围：

{从 .spec/next-version-input.md 概述五源汇总结果}

让我们确认一下 v{N} 具体要做什么。
```

## Cross-Version Role Context Loading

Each role in v2 receives a Role Context Guide that distinguishes between
baseline content and v2-specific content:

```
### Current Role: {role} — Stage {N}: {stage name} (v2)

**Must Read** (v2-specific):
1. `.spec/{v2-relevant-spec}` Section "v2 Additions" — {what's new}

**Must Read** (baseline, from handoff):
2. `.spec/handoffs/stage{N-1}_{name}.md` — {v2 handoff note}

**Baseline Reference** (read specific sections when needed):
3. `.spec/{baseline-spec}` — {which sections to reference for context}

**Archive** (only when explicitly needed):
4. `.archive/v1/{file}` — {when to read this, e.g., "for regression test cases"}

**Skip**:
- `.archive/v1/handoffs/*` — v1 specific, not relevant
```

## Version Lifecycle Summary

```
v1: Stage 0 → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10
                                                    ↓
                                               Archive v1
                                               Evolve baselines
                                               Reset PROJECT_STATE
                                                    ↓
v2: Stage 0 (streamlined) → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10
                                                                  ↓
                                                             Archive v2
                                                             Evolve baselines
                                                                  ↓
v3: ...
```

Each version's Stage 9 (UAT) produces `.spec/next-version-input.md`,
which becomes the primary input for the next version's Stage 1.
