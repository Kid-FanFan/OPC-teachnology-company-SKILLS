# Shared Role Templates

Common templates used by all role definitions. Each role file references these instead of
duplicating the full template.

## Handoff Note Template

Every role writes a handoff note at stage exit using this template. The note goes to
`.spec/handoffs/stage{N}_{stage_name}.md`.

```markdown
# Stage {N}: {current_stage_name} → {next_stage_name}

## 给 {next_role_name}

### 核心产出
- {1-3 bullet points about what was produced and where}

### 关键决策
- {2-5 bullet points about decisions the next role needs to know about}

### 下一步指引
- {What the next role should focus on first}
- {Any known issues or concerns to watch for}

### 参考文件
- 详情：`.spec/{current_stage_spec_file}`
- 相关：{other files the next role might need}
```

Keep the handoff note under 50 lines. The next role reads this INSTEAD of your full spec file
on first load — they will read the full spec only if they need details.

## Context Loading Pattern

Every role must follow this progressive loading pattern:

1. Read `PROJECT_STATE.md` Role Context Guide section first — it tells you exactly which files
   are must-read, reference, or skippable.
2. Read the handoff note from the previous role (`.spec/handoffs/`) as your primary context —
   read it before any full spec files.
3. Only read full spec files when the handoff note indicates you need details.

## External Tool Integration Pattern

Before starting work, check PROJECT_STATE.md "Environment Capabilities" section.
If tools relevant to your stage are available, integrate them into your workflow.
If not available, proceed with the standard approach (text-based deliverables only).

Common integrations:

- **Stage 2 (UI Design)**: If `pencil MCP` available, use it for visual mockups
- **Stage 6 (Frontend)**: If `frontend-design` skill available, use it for complex UI components
- **Stage 7 (Testing)**: If `webapp-testing` skill available, run actual browser tests
- **Stage 7 (Security)**: If `security-review` skill available, run automated security scan

## Downstream Alerts Format

When exiting a stage, summarize for downstream roles using this format:

```markdown
## 下游提示
- 给 {role}: {what they need to know}
- 给 {role}: {what they need to know}
```

## Shared Section Reference

Every role file uses the following shorthand instead of repeating full sections:

- **Handoff Note**: `遵循 references/role_common.md 中的 Handoff Note Template。`
  Followed by the output file path, e.g., `.spec/handoffs/stage{N}_{name}.md`
- **External Tool Integration**: `遵循 references/role_common.md 中的 External Tool Integration Pattern。`
- **Downstream Alerts**: `遵循 references/role_common.md 中的 Downstream Alerts Format。`
  Followed by role-specific alert items.
- **HTML Report**: `遵循 references/role_common.md 中的 HTML Report Pattern。`
  Only for roles in the eligibility matrix (see below).

## HTML Report Pattern

For stages that produce design proposals or reports (not source code or config files),
an additional HTML file is generated for human visualization. The Markdown deliverable
is always generated first and remains the primary artifact for AI agents.

**Core principle**: Markdown is for AI agents, HTML is for humans. Both always exist.

**Eligible stages and output paths** (see `references/html_report_template.md` for full matrix):
- Stage 1 (PRD): `.doc/html_report/prd.html`
- Stage 2 (UI Design): `.doc/html_report/ui-design.html`
- Stage 3 (Architecture): `.doc/html_report/architecture.html`
- Stage 4 (Database): `.doc/html_report/database.html`
- Stage 7 (Code Review): `.doc/html_report/code-review.html`
- Stage 9a (Test Report): `.doc/html_report/test-report.html`
- Stage 9a (Security Audit): `.doc/html_report/security-audit.html`
- Stage 10 (UAT Report): `.doc/html_report/uat.html`

**Generation workflow**:
1. Complete the standard Markdown deliverable first (do NOT skip it)
2. Read `references/html_report_template.md` for the HTML skeleton, CSS styles, and Mermaid examples
3. Transform the Markdown content into HTML using the base template
4. Create `.doc/html_report/` directory if it does not exist
5. Write the HTML file to the specified output path
6. Add the HTML file to PROJECT_STATE.md Deliverables Index with type `html_report`

**What goes in the HTML**: Rich visual formatting, color-coded tables, Mermaid diagrams
(flow charts, ER diagrams, architecture diagrams, pie charts), status badges, summary stats.

**What stays in Markdown only**: Source code, config files, startup guides, deployment procedures.
