# Project Initialization Guide

The initialization phase gathers enough information to set up the project properly. This is
driven by the Project Manager role through a multi-round dialogue with the user.

## Initialization Flow

### Round 1: Project Vision

向用户询问：
1. **项目名称** — 用作根目录名和文档标题
2. **一段话描述** — 这个应用是什么？给谁用的？
3. **目标用户** — 终端用户、内部团队、客户等

根据回答，复述确认理解是否正确。

### Round 2: Scope and Features

向用户询问：
1. **核心功能** — 列出必须实现的功能（3-8项）
2. **可选功能** — 可以延后的功能
3. **规模预期** — 多少用户？多少数据？什么量级的访问？

提出功能优先级矩阵（Must / Should / Could / Won't）供用户审阅。

### Round 3: Technical Preferences

向用户询问：
1. **技术栈偏好** — 有没有指定的语言、框架或平台？
   如果没有偏好，根据项目类型推荐。
2. **部署目标** — 云端（AWS/Azure/GCP）、本地、混合？
3. **集成需求** — 外部 API、遗留系统、第三方服务？

如果用户没有强烈偏好，提出合适的技术栈并说明理由。

### Round 4: Development Model Selection

根据收集到的信息，推荐开发模型：

| 如果项目是... | 推荐 | 原因 |
|--------------|------|------|
| 小范围、需求明确、4周以内 | 瀑布模型 | 足够简单，一次通过 |
| 大型但可按模块拆分 | 增量模型 | 逐模块交付价值 |
| 需求可能根据反馈变化 | 迭代模型 | 多轮细化 |
| 高技术风险或新领域 | 螺旋模型 | 每轮先做风险评估 |
| 需求快速变化、用户反馈驱动 | Scrum | 时间盒、灵活适应 |

解释推荐理由并获得用户确认。如果用户倾向其他模型，使用用户的选择。

### Round 5: Timeline and Constraints

向用户询问：
1. **时间线** — 有硬性截止日期还是灵活的？
2. **团队约束** — 有没有不需要的角色？
3. **质量标准** — 原型/MVP 还是生产就绪？

### Initialization Output

After all rounds are complete, produce the following:

1. **Create project directory structure** under the specified project root:
   ```
   mkdir -p .spec/handoffs .claude/rules docs database backend frontend devops
   ```
2. **Write `docs/project-charter.md`** with all gathered information
3. **Write `CLAUDE.md`** at project root — auto-generated AI project instructions (see below)
4. **Write `PROJECT_STATE.md`** with initial state including Role Context Guide for Stage 1
   (see state_management.md)
5. **Write subdirectory CLAUDE.md files** for progressive loading (see below)
6. **Write `.claude/rules/` path-scoped rules** for auto-triggered context (see below)
7. **Fork Environment Detection SubAgent** — detect available skills and MCP tools in the
   user's environment, store results in PROJECT_STATE.md "Environment Capabilities" section
   (see `references/env_detection.md`)
8. **Display a summary** of the project setup for final user confirmation

### CLAUDE.md Auto-Generation

During initialization, automatically generate a `CLAUDE.md` file at the project root.
This file is read by AI coding agents at the start of every conversation, ensuring all
subsequent coding work follows the project's conventions automatically.

**CLAUDE.md Template:**

```markdown
# {project_name}

{one-line project description}

## Tech Stack
- Frontend: {framework + language}
- Backend: {framework + language}
- Database: {engine}
- Deployment: {target}

## Project Structure
- `.spec/` — Development constraint specifications (AI reads these before coding)
- `docs/` — Human-facing documentation
- `backend/` — Backend source code
- `frontend/` — Frontend source code
- `database/` — Schema DDL and seed data
- `devops/` — Deployment configuration

## Coding Conventions
- {language}-specific conventions based on tech stack choice}
- Error handling: {approach determined during initialization}
- API style: {REST/GraphQL, response format}
- Naming conventions: {determined by tech stack}

## Development Workflow
1. Read relevant `.spec/` files before implementing features
2. Follow the architecture defined in `.spec/architecture.md`
3. API endpoints must conform to `.spec/api-spec.md`
4. UI implementation must follow `.spec/ui-design.md`
5. Database changes must align with `.spec/database.md`

## Key Decisions
| Decision | Choice | Reason |
|----------|--------|--------|
| Dev Model | {model} | {rationale} |
| {other key decisions from init} | | |
```

### Subdirectory CLAUDE.md Generation

These files are lazy-loaded by Claude Code when a role accesses files in the corresponding
directory. They contain role-specific conventions and point to the relevant spec subsets.

**`backend/CLAUDE.md` Template:**
```markdown
# Backend Development

## 实现前必读
- `.spec/api-spec.md` — 接口契约（端点、请求/响应格式）
- `database/schema.sql` — 表结构（字段名、类型、外键）

## 编码规范
- {Tech stack specific patterns — e.g., Controller → Service → Mapper}
- {Error handling approach}
- {Response format standard}

## 目录约定
- {Framework-specific directory structure}
```

**`frontend/CLAUDE.md` Template:**
```markdown
# Frontend Development

## 实现前必读
- `.spec/ui-design.md` — 页面布局、设计系统、交互流程
- `.spec/api-spec.md` — 可用的 API 端点和数据契约

## 编码规范
- {Framework specific patterns — e.g., component structure, state management}
- {Styling approach — CSS modules / Tailwind / styled-components}
- {Responsive design breakpoints}

## 目录约定
- {Framework-specific directory structure}
```

**`database/CLAUDE.md` Template:**
```markdown
# Database Development

## 操作前必读
- `.spec/database.md` — ER 图、实体关系、索引策略
- `.spec/api-spec.md` — API 查询模式（影响索引设计）

## 规范
- 命名：{snake_case / camelCase}
- 主键策略：{auto-increment / UUID}
- 迁移方式：{manual DDL / migration tool}
```

**`devops/CLAUDE.md` Template:**
```markdown
# DevOps

## 配置前必读
- `.spec/architecture.md` — 部署架构、服务依赖
- `backend/README.md` — 后端运行时需求
- `frontend/README.md` — 前端构建流程

## 规范
- 容器基础镜像：{distroless / alpine}
- CI/CD 平台：{GitHub Actions / GitLab CI}
```

### Path-Scoped Rules Generation

These rules in `.claude/rules/` are automatically triggered by Claude Code when the role
edits files matching the path pattern. They inject just-in-time reminders without consuming
context when not needed.

**`.claude/rules/api-standards.md`:**
```markdown
---
paths: ["backend/**/controller/**", "backend/**/handler/**"]
---

编写 API 处理代码时，参考 `.spec/api-spec.md` 中对应端点的请求/响应格式。
确保参数校验、错误码、响应结构与 API 规范一致。
```

**`.claude/rules/db-conventions.md`:**
```markdown
---
paths: ["database/**", "backend/**/mapper/**", "backend/**/repository/**"]
---

操作数据库时，参考 `database/schema.sql` 的表结构和 `database/data_dict.md` 的字段说明。
命名遵循 database/CLAUDE.md 中的规范。
```

**`.claude/rules/frontend-rules.md`:**
```markdown
---
paths: ["frontend/**"]
---

编写前端代码时，UI 实现遵循 `.spec/ui-design.md` 的设计系统和页面布局。
API 调用遵循 `.spec/api-spec.md` 的端点和数据格式。
```

**`.claude/rules/spec-reminder.md`:**
```markdown
---
paths: [".spec/**"]
---

修改 .spec/ 文件后，检查是否需要同步更新下游规范或 CLAUDE.md 中的引用。
重大变更需在 PROJECT_STATE.md 的 Key Decisions Log 中记录。
```

### Project Charter Template → `docs/project-charter.md`

```markdown
# 项目章程：{project_name}

## 项目愿景
{一段话描述}

## 目标用户
{用户描述}

## 功能优先级矩阵
| 优先级 | 功能 | 描述 |
|--------|------|------|
| 必须有 | ... | ... |
| 应该有 | ... | ... |
| 可以有 | ... | ... |
| 本次不做 | ... | ... |

## 技术栈
- 前端：{tech}
- 后端：{tech}
- 数据库：{tech}
- 部署：{target}

## 开发模型
{模型名称} — {选择理由}

## 周期/迭代计划
{如何将工作拆分为周期或迭代}

## 时间线
{目标日期或工期}

## 约束条件
{已知的约束}

## 关键决策
| 决策 | 选择 | 原因 |
|------|------|------|
```
