# dmtab-teachnology-company

一个 Claude Code Skill，模拟虚拟软件公司，通过 10 个专业角色协作驱动应用从需求到部署的完整开发流程。

## Quick Start

```bash
# 1. 安装
cp -r dmtab-teachnology-company ~/.claude/skills/

# 2. 进入你的项目目录，启动 Claude Code
cd your-project && claude

# 3. 启动 Skill
/dmtab-teachnology-company

# 4. 完成 5 轮初始化问答后，推进阶段
/dmtab-teachnology-company next

# 5. 查看当前状态
/dmtab-teachnology-company status
```

最小流程：初始化 → 需求 → UI → 架构 → 数据库 → 后端 → 前端 → 代码审查 → 部署 → 测试 → 用户验收 → 生产部署。每个阶段建议开新会话。

## 概述

dmtab-teachnology-company 将 Claude 变成一个虚拟开发团队。每个阶段由对应的专业角色主导，以角色身份与用户对话、产出交付物，完成后交接给下一个角色。

```
用户提出需求 → 引导式初始化 → 需求探索+分析 → UI设计 → 架构设计 → 数据库设计 → 后端/前端开发 → 代码审查 → 环境部署 → 测试+安全审计+Bug修复 → 用户验收测试 → 生产部署
```

## 角色阵容

| 角色 | 阶段 | 核心交付物 |
|------|------|-----------|
| 项目经理 | 初始化 + 全程协调 | 项目章程、环境检测、进度报告 |
| 产品经理 | 需求分析 + 用户验收测试 | PRD（含用户故事、业务功能架构、优先级矩阵）、功能使用手册、UAT报告 |
| UI设计师 | UI设计 | 页面结构、交互流程、设计规范 |
| 技术架构师 | 架构设计 + 代码审查 | 技术架构文档、API规范、模块划分、并行策略评估、代码审查报告 |
| 数据库工程师 | 数据库设计 | ER图、DDL脚本、数据字典 |
| 后端工程师 | 后端开发 | 后端源码 |
| 前端工程师 | 前端开发 | 前端源码 |
| 测试工程师 | 测试 + Bug修复协调 | 测试用例、测试报告、Bug修复协调、回归验证 |
| 安全工程师 | 安全审计 | 安全审计报告 |
| DevOps工程师 | 环境部署 + 生产部署 | 部署配置、启动指南、CI/CD、生产部署文档 |

## 需求到架构的链路

Skill 通过三层递进确保需求不丢失：

```
产品经理产出                    技术架构师消费
─────────────                  ──────────────
用户故事          ──────→      每个用户故事有对应 API 端点
业务功能架构      ──────→      映射为技术模块（可能合并/拆分）
优先级矩阵        ──────→      决定实现顺序和并行策略
```

### 业务功能架构 vs 技术架构

| 维度 | 业务功能架构（产品经理产出） | 技术架构（架构师产出） |
|------|---------------------------|---------------------|
| 受众 | 业务方、产品经理、用户 | 开发工程师、DevOps |
| 语言 | 业务术语（订单、商品、用户） | 技术术语（微服务、API、数据库表） |
| 内容 | 业务模块划分、业务流程、角色权限 | 技术选型、模块通信、API设计、数据库 |
| 变更频率 | 随业务需求变化 | 相对稳定 |

产品经理在 PRD 中产出**业务功能架构**（Business Module Map），包含模块清单、业务流程和角色权限矩阵。架构师以此为输入，通过映射表将业务模块转化为技术模块。

## 目录导航

| 主题 | 位置 |
|------|------|
| 快速开始 | 上方 Quick Start |
| 开发模型 | `references/dev_models.md` |
| 项目初始化 | `references/project_init.md` |
| 状态管理 | `references/state_management.md` |
| 目录结构与上下文加载 | `references/architecture_overview.md` |
| SubAgent 并行模式 | `references/subagent_patterns.md` |
| 跨版本迭代 | `references/version_transition.md` |
| 各阶段交付物模板 | `references/stage_templates.md` |
| 角色定义 | `references/roles/*.md` |

## 支持的开发模型

根据项目特点自动推荐，也可手动选择：

| 模型 | 适用场景 |
|------|---------|
| 瀑布模型 | 需求明确、小项目、一次性交付 |
| 增量模型 | 可按模块拆分、需要分批交付价值 |
| 迭代模型 | 需求不明确、需要用户反馈逐步细化 |
| 螺旋模型 | 高技术风险、大型项目 |
| Scrum | 需求快速变化、用户反馈驱动 |

项目级按选定模型组织，落到每个功能点内部仍按标准瀑布流程执行。

## 安装

将 `dmtab-teachnology-company` 目录复制到 Claude Code 的 skills 路径下：

```bash
# 方式一：复制到用户级 skills 目录
cp -r dmtab-teachnology-company ~/.claude/skills/

# 方式二：复制到项目级 .claude/skills 目录
cp -r dmtab-teachnology-company your-project/.claude/skills/
```

## 使用方式

### 启动新项目

```
/dmtab-teachnology-company
```

启动后进入引导式初始化，通过多轮问答收集项目信息：

1. **项目愿景** — 名称、描述、目标用户
2. **范围和功能** — 核心功能、优先级、规模预期
3. **技术偏好** — 技术栈、部署目标、集成需求
4. **开发模型** — 根据项目特征推荐并确认
5. **时间线和约束** — 截止时间、质量标准
6. **环境检测** — 自动检测可用的外部 Skill 和 MCP 工具

### 流程控制命令

每个命令采用**按需加载**策略，只读取必要的文件，不加载全部内容：

| 命令 | 说明 | 加载内容 |
|------|------|---------|
| `/dmtab-teachnology-company` | 新项目或恢复 | 检测 PROJECT_STATE.md 决定 |
| `/dmtab-teachnology-company status` | 查看当前状态 | 仅 PROJECT_STATE.md |
| `/dmtab-teachnology-company next` | 完成当前阶段 | 当前角色文件 + 状态 |
| `/dmtab-teachnology-company rollback <N>` | 回退到指定阶段 | 状态 + stage_templates |
| `/dmtab-teachnology-company role <角色名>` | 切换角色（异常模式） | 目标角色文件 + 状态 |
| `/dmtab-teachnology-company review` | 项目经理视角汇总 | 仅 PROJECT_STATE.md |
| `/dmtab-teachnology-company deliverables` | 查看交付物清单 | 仅 Deliverables Index |

### 角色切换机制

**正常模式**：阶段按顺序交接，当前角色完成后自动切换到下一角色。

**异常模式**：遇到问题时可随时切换到相关角色处理：

| 触发场景 | 切换到 |
|---------|--------|
| 测试发现设计缺陷 | UI设计师 / 技术架构师 |
| 开发中发现需求不明确 | 产品经理 |
| 数据库设计影响性能 | 数据库工程师 + 技术架构师 |
| 安全审计发现漏洞 | 安全工程师 + 对应开发角色 |
| 部署环境与架构不匹配 | DevOps + 技术架构师 |
| 任意阶段发现进度风险 | 项目经理 |

异常处理完成后自动回到被中断的阶段继续。

## 项目目录结构

以项目名为根目录，自动生成如下结构。采用 `.spec/` + `docs/` 分离策略，并通过五层渐进式上下文机制防止上下文爆炸：

### 目录职责

| 目录/文件 | 消费者 | 加载时机 | 作用 |
|-----------|--------|---------|------|
| `CLAUDE.md` | AI 智能体 | 每次对话自动加载 | 项目级规则（技术栈、编码规范），<100行 |
| `.spec/` | AI 编码智能体 | 按需加载 | AI 约束规范（要做什么），英文 |
| `.spec/handoffs/` | 下一角色 | 角色上岗时加载 | 精简交接笔记（<50行，渐进式上下文核心） |
| `{dir}/CLAUDE.md` | AI 智能体 | 访问该目录时懒加载 | 角色专属编码约定 |
| `.claude/rules/` | AI 智能体 | 编辑匹配文件时自动触发 | 路径作用域规则 |
| `docs/` | 人类 | 手动查阅 | 人类文档（中文） |
| `.doc/html_report/` | 人类（浏览器） | 角色生成 | 方案设计和报告的 HTML 可视化报告（CSS + Mermaid.js 图表） |
| `PROJECT_STATE.md` | Skill 引擎 | 每次首先读取 | 项目状态 + 角色阅读指南 + 环境工具检测 |
| `.archive/` | AI 智能体（按需） | 显式引用时 | 已完成版本的归档快照（按需查阅，不自动加载） |
| `database/migrations/` | DB 工程师 / DevOps | v2+ 数据库变更时 | 版本增量迁移脚本（幂等，升级部署用） |

### 完整目录结构

```
project_name/
├── CLAUDE.md                        # 项目级 AI 指令（<100行，每次自动加载）
├── PROJECT_STATE.md                 # 项目状态 + 角色阅读指南 + 环境工具能力
├── .doc/                            # HTML 可视化报告（浏览器打开，自包含）
│   └── html_report/
│       ├── prd.html                 # PRD 可视化报告（业务流程图、优先级矩阵）
│       ├── ui-design.html           # UI 设计报告（页面导航流）
│       ├── architecture.html        # 架构报告（系统架构图、模块依赖图）
│       ├── database.html            # 数据库报告（ER 实体关系图）
│       ├── code-review.html         # 代码审查报告
│       ├── test-report.html         # 测试报告（Bug 分布图）
│       ├── security-audit.html      # 安全审计报告（攻击面映射）
│       └── uat.html                 # UAT 报告
├── .spec/                           # AI 约束规范（英文）
│   ├── prd.md                       # 产品需求（含业务功能架构）
│   ├── ui-design.md                 # UI 设计规范
│   ├── architecture.md              # 系统架构（含并行策略评估）
│   ├── api-spec.md                  # API 规范
│   ├── database.md                  # 数据库设计
│   ├── test-cases.md                # 测试用例
│   ├── next-version-input.md        # 下一版本输入（五源汇总）
│   └── handoffs/                    # 角色交接笔记
│       ├── stage0_initialization.md
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
├── .claude/rules/                   # 路径作用域规则（编辑文件时自动触发）
│   ├── api-standards.md             # 编辑 Controller 时提醒参考 API 规范
│   ├── db-conventions.md            # 编辑数据库文件时提醒参考 schema
│   ├── frontend-rules.md            # 编辑前端时提醒参考设计规范
│   └── spec-reminder.md             # 编辑 .spec/ 时提醒同步更新
├── docs/                            # 人类文档（中文）
│   ├── project-charter.md           # 项目章程
│   ├── user-guide.md                # 功能使用手册
│   ├── release-notes.md             # 版本发布说明
│   ├── uat-report.md                # UAT 报告
│   ├── deployment-guide.md          # 部署指南
│   ├── security-audit.md            # 安全审计报告
│   └── test-report.md               # 测试总结报告
├── database/
│   ├── CLAUDE.md                    # 数据库约定（访问 database/ 时懒加载）
│   ├── schema.sql                   # DDL 建表脚本（始终是最新完整快照）
│   ├── migrations/                  # 版本迁移脚本（v2+ 生成，升级部署用）
│   ├── seed_data.sql                # 种子数据
│   └── data_dict.md                 # 数据字典
├── backend/
│   ├── CLAUDE.md                    # 后端约定（访问 backend/ 时懒加载）
│   └── ...                          # 后端源码
├── frontend/
│   ├── CLAUDE.md                    # 前端约定（访问 frontend/ 时懒加载）
│   └── ...                          # 前端源码
└── devops/
    ├── CLAUDE.md                    # DevOps 约定（访问 devops/ 时懒加载）
    ├── Dockerfile
    ├── docker-compose.yml
    └── ci.yml
```

### 五层渐进式上下文加载

后端工程师上岗时，不需要读完前面所有 spec 文件。通过以下五层机制精准加载：

```
第一层：Role Context Guide（PROJECT_STATE.md 中）
  → 告诉角色"必读什么、参考什么、跳过什么"
  → 后端工程师：必读 api-spec.md + schema.sql，跳过 ui-design.md + prd.md

第二层：Handoff Note（.spec/handoffs/ 目录）
  → 上一角色写的精简交接笔记（< 50 行），代替读完整 spec 文件
  → 架构师→数据库工程师的交接笔记包含实体数量、选型、关键索引需求

第三层：子目录 CLAUDE.md（backend/、frontend/ 等目录）
  → Claude Code 访问该目录时自动懒加载，包含角色专属编码约定

第四层：.claude/rules/ 路径作用域规则
  → 编辑 Controller 时自动提醒"参考 .spec/api-spec.md"
  → 零配置、自动触发、不需要时不消耗上下文

第五层：Subagent 聚合（按需使用）
  → 需要全面了解系统时（如测试工程师），启动子智能体在独立上下文中读所有 spec
  → 子智能体返回精简摘要，主上下文不膨胀
```

## 环境检测机制

Skill 在初始化时通过 SubAgent 自动检测用户环境中可用的外部 Skill 和 MCP 工具：

```
初始化阶段 → Fork 环境检测 SubAgent
  → 扫描可用 Skill（webapp-testing、security-review、frontend-design 等）
  → 扫描可用 MCP 工具（Pencil 等）
  → 生成精简报告（<15行）存入 PROJECT_STATE.md
```

角色上岗时检查环境能力，条件性调用可用工具：

| 角色 | 可利用的外部工具 | 用途 |
|------|---------------|------|
| UI 设计师 | Pencil MCP | 生成可视化设计稿 |
| 前端工程师 | frontend-design Skill | 生成高质量 UI 组件 |
| 测试工程师 | webapp-testing Skill | Playwright 浏览器自动化测试 |
| 安全工程师 | security-review Skill | 自动化代码安全扫描 |

不硬编码外部工具依赖——未安装时自动回退到标准文本方式。

## Fork SubAgent 并行开发机制

Claude Code 的 Fork SubAgent 创建隔离的上下文窗口，共享父级上下文前缀（最大化 Prompt Cache 命中）。架构师通过**并行策略评估**决定是否启用：

| 策略 | 条件 | 场景 |
|------|------|------|
| 顺序开发 | < 3 个模块 | 无并行 |
| 前后端并行 | 3-4 模块，> 15 个 API 端点 | Scenario A |
| 全并行 | >= 5 模块，>= 2 个 Tier-1 模块 | Scenario A+B |

### 场景 A：前后端并行

```
阶段 1: 冻结合同 — 标记 .spec/api-spec.md 为 FROZEN
阶段 2: Fork 两个并行 SubAgent（后端 + 前端）
阶段 3: Fork 验证 SubAgent — 交叉比对实现的接口一致性
阶段 4: 主对话根据验证报告修复不一致
```

### 场景 B：模块级并行

```
阶段 1: Fork 单个 SubAgent 生成共享层（Entity、Response 封装、中间件）
阶段 2: 按依赖图分层 — Tier 1 并行 → Tier 2 并行 → Tier 3 串行
阶段 3: Fork 集成验证 SubAgent — 检查跨模块调用和类型一致性
```

详细模式和任务指令模板见 `references/subagent_patterns.md`。

### 场景 F：测试阶段 Bug 修复协调

测试工程师发现 Critical/High 级 Bug 后，通过 SubAgent 协调修复，不离开测试角色：

```
测试执行 → Bug 分级（三重分类：严重度 + 责任归属 + 决策类型）
  ↓
预检（按需）：
  需求歧义？ → Fork 产品经理 SubAgent 澄清
  架构影响？ → Fork 架构师 SubAgent 评估修复方向
  ↓
修复（并行）：
  后端 Bug → Fork 后端修复 SubAgent
  前端 Bug → Fork 前端修复 SubAgent
  安全 Bug → Fork 安全修复 SubAgent
  ↓
大规模评估（按需）：
  阻塞 Bug > 5 个？ → Fork 项目经理 SubAgent 评估进度风险
  ↓
审查合并 → 回归验证 → Bug 全部清零 → 交接 DevOps
```

Stage 9a 不在所有 Critical/High Bug 修复并验证通过前结束。Medium/Low 级 Bug 记入 Backlog，不阻塞部署。

### 场景 G：环境部署 → 用户验收测试（Stage 8→10）

代码审查通过后，先由 DevOps 部署运行环境让应用可访问，测试工程师在已部署的应用上测试，再由产品经理引导用户试用：

```
Stage 8: DevOps 部署运行环境（本地/Docker/K8s/云，用户选择）
  → 应用成功启动并可访问
  ↓
Stage 9a: 测试工程师在已部署应用上执行功能测试 + 安全审计
  → Critical/High Bug → Stage 9b 修复 → 回归验证
  → 全部通过 → 交接产品经理
  ↓
Stage 10: 产品经理引导用户试用
  → 收集反馈（Bug / 体验 / 功能建议）
  → Critical/High Bug → 回到测试修复
  → 用户签收确认 → 汇总五源数据产出下一版本输入
  ↓
Stage 11: DevOps 准备生产部署文档和配置
```

下一版本输入的五个来源：PRD 延迟项、项目愿景、UAT 用户反馈、已知问题 Backlog、架构演进建议。

## 会话管理建议

### 推荐做法：每个阶段一个新会话

每个阶段完成并执行 `/dmtab-teachnology-company next` 后，Skill 会提示你开启新会话：

```bash
/exit                                    # 结束当前会话
claude                                   # 在同一目录启动新会话
/dmtab-teachnology-company               # 从 PROJECT_STATE.md 恢复进度
```

**为什么有效**：新会话只加载 CLAUDE.md（<100行）+ PROJECT_STATE.md（冷启动文件）+ 交接笔记（<50行），前阶段的所有噪音自动清除。

### 何时必须开新会话

| 场景 | 原因 |
|------|------|
| 阶段切换（`next` 后） | 上一角色的上下文对下一角色无用且有害 |
| 回退（`rollback`） | 当前上下文可能包含基于已回退内容的决策 |
| 上下文明显退化 | AI 开始遗忘早期决策或重复自身时 |

阶段内可用 `/compact` 压缩上下文，但注意是压缩而非重置。

## 跨版本迭代

部署完成后，项目通过**版本归档 + 基线演进**机制进入下一迭代。

### 文档三分法

| 类别 | 处理方式 | 示例 |
|------|---------|------|
| 基线规范 | 保留并演进（v2 在 v1 基础上追加） | prd.md, api-spec.md, architecture.md |
| 持久文档 | 保留（跨版本有效） | CLAUDE.md, project-charter.md |
| 版本快照 | 归档到 `.archive/v{N}/` | handoffs/*, test-report.md, uat-report.md |

### 版本生命周期

```
v1: Stage 0→11 → 归档 v1 快照 → 演进基线规范 → 重置 PROJECT_STATE
                                               ↓
v2: Stage 0（精简 3 轮）→ 1→11 → 归档 v2 → ...
```

### v2+ 精简初始化

v2 的 Stage 0 只需 3 轮（v1 是 6 轮）：

1. **范围确认** — 基于 `.spec/next-version-input.md`（UAT 五源汇总），用户确认 v2 范围
2. **技术变更** — 技术栈/模型是否调整？无变更则跳过
3. **环境重检** — 检测是否有新工具可用

### 基线规范演进

v2 在基线规范文件中追加内容，不覆盖 v1：

```
api-spec.md:
  ┌─ v1 原有端点（不变）
  └─ ## v2 Additions
      └─ 新增端点定义
```

### 数据库迁移

v2 新增 `database/migrations/` 目录，记录增量变更：

```
database/
├── schema.sql           # 始终是最新完整 DDL（可用于全新部署）
├── migrations/
│   └── v2_001_add_comments.sql   # 增量 ALTER/CREATE（用于升级部署）
└── seed_data.sql
```

### 目录结构（多版本累积后）

```
project_name/
├── .spec/                    # 基线规范（持续演进）
├── .archive/                 # 归档（按版本）
│   ├── v1/
│   │   ├── docs/             # test-report, security-audit, uat-report, etc.
│   │   ├── handoffs/         # stage0~10 交接笔记
│   │   └── test-cases.md
│   └── v2/
│       └── ...
├── database/
│   ├── schema.sql            # 最新完整 DDL
│   ├── migrations/           # 版本迁移脚本
│   └── seed_data.sql
├── backend/                  # 代码（跨版本积累）
├── frontend/
└── devops/
```

详细策略见 `references/version_transition.md`。

## Skill 文件结构

```
dmtab-teachnology-company/
├── SKILL.md                          # Skill 主入口（~140行，精简）
├── README.md                         # 本文档
└── references/
    ├── project_init.md               # 项目初始化引导流程（含环境检测）
    ├── dev_models.md                 # 开发模型定义与选择决策树
    ├── state_management.md           # 状态管理规范（含环境能力章节）
    ├── stage_templates.md            # 各阶段交付物模板与完成标准
    ├── architecture_overview.md      # 目录结构、渐进式加载、SubAgent、角色切换详解
    ├── subagent_patterns.md          # Subagent 并行模式、策略评估、任务指令模板
    ├── role_common.md                # 角色共享模板（交接笔记、下游提示、外部工具集成）
    ├── env_detection.md              # 环境检测 SubAgent 任务指令
    ├── html_report_template.md       # HTML 可视化报告共享模板（CSS + Mermaid 图表）
    ├── version_transition.md         # 跨版本归档、基线演进、数据库迁移、v2+ 精简初始化
    └── roles/
        ├── project_manager.md        # 项目经理：初始化、进度跟踪、风险协调
        ├── product_manager.md        # 产品经理：需求探索、用户故事、业务功能架构、用户验收测试
        ├── ui_designer.md            # UI设计师：页面结构、设计系统、交互流程
        ├── architect.md              # 技术架构师：技术架构、API设计、并行策略评估、代码审查
        ├── db_engineer.md            # 数据库工程师：数据模型、DDL、索引策略
        ├── backend_engineer.md       # 后端工程师：API实现、业务逻辑、SubAgent并行
        ├── frontend_engineer.md      # 前端工程师：UI实现、API对接、SubAgent并行
        ├── test_engineer.md          # 测试工程师：测试计划、用例执行、Bug修复协调、回归验证
        ├── security_engineer.md      # 安全工程师：OWASP审计、漏洞扫描
        └── devops_engineer.md        # DevOps工程师：容器化、CI/CD、部署文档
```

## 设计理念

- **角色沉浸** — 每个角色有自己的视角、优先级和说话风格，模拟真实团队中不同岗位的思维差异
- **交付物驱动** — 每个阶段必须产出具体文件（文档或代码），不允许只描述不交付。方案和报告类文档额外生成 HTML 可视化版本（`.doc/html_report/`），供人在浏览器中直观查看
- **渐进式上下文** — 角色不读所有前序文件，而是按阅读指南精准加载必读内容 + 交接笔记，按需查细节，防止上下文爆炸
- **用户决策** — 角色提建议、做推荐，但所有关键决策由用户确认
- **灵活回退** — 支持回退到任意阶段重新来过，也支持随时切换角色处理问题
- **会话边界感知** — 每个阶段是天然的会话边界，通过 PROJECT_STATE.md 实现跨会话状态传递
- **环境自适应** — 运行时检测可用工具，条件性调用，不硬编码外部依赖
- **需求到架构的桥接** — 产品经理产出业务功能架构，架构师消费并映射为技术架构，确保需求不丢失
- **测试闭环** — 测试不仅是发现问题，还包括协调修复和回归验证，Stage 9a 在所有阻塞级 Bug 清零后才完成
- **先部署后测试** — 代码开发完成后先由架构师审查代码质量（Stage 7），再由 DevOps 部署运行环境（Stage 8），测试工程师在已部署的应用上执行功能测试（Stage 9a），产品经理引导用户试用（Stage 10），最后准备生产部署（Stage 11）
- **多方式部署** — DevOps 工程师提供本地直跑、Docker Compose、Kubernetes、云托管四种部署方式，用户根据场景选择
- **跨版本持续迭代** — 部署后归档版本快照、演进基线规范、重置项目状态，v2+ 精简初始化，数据库通过迁移脚本管理增量变更
