# HTML Report Template Reference

This file defines the shared HTML template and guidelines for generating visual reports.
Roles reference this file when they need to produce HTML reports alongside their Markdown deliverables.

**Core principle**: Markdown (`.spec/`, `docs/`) is for AI agents. HTML (`.doc/html_report/`) is for humans.
Both are always generated — HTML is an addition, not a replacement.

## Eligibility Matrix

| Stage | Role | HTML Output | MD Source |
|-------|------|-------------|-----------|
| 1 | Product Manager | `.doc/html_report/prd.html` | `.spec/prd.md` |
| 2 | UI Designer | `.doc/html_report/ui-design.html` | `.spec/ui-design.md` |
| 3 | Tech Architect | `.doc/html_report/architecture.html` | `.spec/architecture.md` |
| 4 | DB Engineer | `.doc/html_report/database.html` | `.spec/database.md` |
| 7 | Tech Architect | `.doc/html_report/code-review.html` | `docs/code-review-report.md` |
| 9a | Test Engineer | `.doc/html_report/test-report.html` | `docs/test-report.md` |
| 9a | Security Engineer | `.doc/html_report/security-audit.html` | `docs/security-audit.md` |
| 10 | Product Manager | `.doc/html_report/uat.html` | `docs/uat-report.md` |

**No HTML** for Stage 0, 5, 6, 8, 9b, 11 (source code, config files, operational guides).

## Generation Workflow

1. Complete the standard Markdown deliverable first (do NOT skip or defer it)
2. Create `.doc/html_report/` directory if it does not exist
3. Read the Markdown source and transform it into HTML using the template below
4. Write the HTML file to the path specified in the eligibility matrix
5. Add the HTML file path to the handoff note's deliverables list
6. Track in PROJECT_STATE.md Deliverables Index with type `html_report`

## Base HTML Template

Below is the complete, self-contained HTML skeleton. Copy and customize the content sections
for each document type. The CSS and JavaScript are embedded — no external dependencies
except Mermaid.js from CDN.

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{document_title} - {project_name}</title>
    <script src="https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.min.js"></script>
    <style>
        :root {
            --primary: #2563eb;
            --primary-light: #dbeafe;
            --success: #16a34a;
            --success-light: #dcfce7;
            --warning: #d97706;
            --warning-light: #fef3c7;
            --danger: #dc2626;
            --danger-light: #fee2e2;
            --info: #0891b2;
            --info-light: #cffafe;
            --bg: #f8fafc;
            --card-bg: #ffffff;
            --text: #1e293b;
            --text-secondary: #64748b;
            --border: #e2e8f0;
            --sidebar-bg: #1e293b;
            --sidebar-text: #f1f5f9;
            --sidebar-active: #2563eb;
            --sidebar-width: 260px;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Noto Sans SC", sans-serif;
            background: var(--bg);
            color: var(--text);
            line-height: 1.7;
            display: flex;
            min-height: 100vh;
        }

        /* Sidebar Navigation */
        nav {
            width: var(--sidebar-width);
            background: var(--sidebar-bg);
            color: var(--sidebar-text);
            padding: 24px 0;
            position: fixed;
            top: 0;
            left: 0;
            bottom: 0;
            overflow-y: auto;
            z-index: 100;
            transition: transform 0.3s ease;
        }

        nav .nav-header {
            padding: 0 20px 20px;
            border-bottom: 1px solid rgba(255,255,255,0.1);
            margin-bottom: 12px;
        }

        nav .nav-header h2 {
            font-size: 16px;
            font-weight: 600;
            color: #fff;
            margin-bottom: 4px;
        }

        nav .nav-header .version {
            font-size: 12px;
            color: var(--text-secondary);
            color: rgba(255,255,255,0.5);
        }

        nav a {
            display: block;
            padding: 8px 20px;
            color: rgba(255,255,255,0.7);
            text-decoration: none;
            font-size: 14px;
            transition: all 0.2s;
            border-left: 3px solid transparent;
        }

        nav a:hover {
            color: #fff;
            background: rgba(255,255,255,0.05);
            border-left-color: var(--primary);
        }

        nav a.active {
            color: #fff;
            background: rgba(37,99,235,0.2);
            border-left-color: var(--primary);
        }

        /* Mobile hamburger */
        .menu-toggle {
            display: none;
            position: fixed;
            top: 12px;
            left: 12px;
            z-index: 200;
            background: var(--primary);
            color: #fff;
            border: none;
            border-radius: 8px;
            padding: 8px 12px;
            font-size: 20px;
            cursor: pointer;
        }

        /* Main Content */
        main {
            margin-left: var(--sidebar-width);
            flex: 1;
            padding: 40px 48px;
            max-width: 960px;
        }

        /* Page Header */
        .page-header {
            margin-bottom: 40px;
            padding-bottom: 24px;
            border-bottom: 2px solid var(--border);
        }

        .page-header h1 {
            font-size: 28px;
            font-weight: 700;
            color: var(--text);
            margin-bottom: 8px;
        }

        .page-header .meta {
            color: var(--text-secondary);
            font-size: 14px;
        }

        /* Sections */
        section {
            margin-bottom: 40px;
        }

        section h2 {
            font-size: 22px;
            font-weight: 600;
            color: var(--text);
            margin-bottom: 16px;
            padding-bottom: 8px;
            border-bottom: 1px solid var(--border);
        }

        section h3 {
            font-size: 18px;
            font-weight: 600;
            color: var(--text);
            margin: 20px 0 12px;
        }

        section h4 {
            font-size: 15px;
            font-weight: 600;
            color: var(--text-secondary);
            margin: 16px 0 8px;
        }

        /* Cards */
        .card {
            background: var(--card-bg);
            border: 1px solid var(--border);
            border-radius: 8px;
            padding: 20px;
            margin-bottom: 16px;
        }

        .card h3 {
            margin-top: 0;
        }

        /* Tables */
        table {
            width: 100%;
            border-collapse: collapse;
            margin: 12px 0 20px;
            font-size: 14px;
            background: var(--card-bg);
            border-radius: 8px;
            overflow: hidden;
            border: 1px solid var(--border);
        }

        th {
            background: #f1f5f9;
            padding: 10px 14px;
            text-align: left;
            font-weight: 600;
            font-size: 13px;
            color: var(--text-secondary);
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        td {
            padding: 10px 14px;
            border-top: 1px solid var(--border);
        }

        tr:hover td {
            background: #f8fafc;
        }

        /* Status Badges */
        .badge {
            display: inline-block;
            padding: 2px 10px;
            border-radius: 12px;
            font-size: 12px;
            font-weight: 600;
        }

        .badge-pass, .badge-success, .badge-yes, .badge-complete {
            background: var(--success-light);
            color: var(--success);
        }

        .badge-fail, .badge-critical, .badge-blocking {
            background: var(--danger-light);
            color: var(--danger);
        }

        .badge-warn, .badge-medium, .badge-conditional {
            background: var(--warning-light);
            color: var(--warning);
        }

        .badge-info, .badge-low, .badge-pending {
            background: var(--info-light);
            color: var(--info);
        }

        .badge-skip, .badge-na {
            background: #f1f5f9;
            color: var(--text-secondary);
        }

        .badge-high {
            background: #fed7aa;
            color: #c2410c;
        }

        /* Severity colors for table cells */
        td.status-pass { background: var(--success-light); }
        td.status-fail { background: var(--danger-light); }
        td.status-warn { background: var(--warning-light); }

        /* Progress Bar */
        .progress-bar {
            background: var(--border);
            border-radius: 8px;
            height: 8px;
            overflow: hidden;
            margin: 8px 0;
        }

        .progress-bar .fill {
            height: 100%;
            border-radius: 8px;
            background: var(--primary);
            transition: width 0.3s;
        }

        .progress-bar .fill.success { background: var(--success); }
        .progress-bar .fill.warning { background: var(--warning); }
        .progress-bar .fill.danger { background: var(--danger); }

        /* Summary Stats */
        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
            gap: 16px;
            margin: 16px 0;
        }

        .stat-card {
            background: var(--card-bg);
            border: 1px solid var(--border);
            border-radius: 8px;
            padding: 16px;
            text-align: center;
        }

        .stat-card .stat-value {
            font-size: 32px;
            font-weight: 700;
            color: var(--primary);
        }

        .stat-card .stat-value.success { color: var(--success); }
        .stat-card .stat-value.danger { color: var(--danger); }
        .stat-card .stat-value.warning { color: var(--warning); }

        .stat-card .stat-label {
            font-size: 13px;
            color: var(--text-secondary);
            margin-top: 4px;
        }

        /* Lists */
        ul, ol {
            padding-left: 24px;
            margin: 8px 0;
        }

        li {
            margin-bottom: 4px;
        }

        /* Code blocks */
        pre {
            background: #1e293b;
            color: #e2e8f0;
            padding: 16px;
            border-radius: 8px;
            overflow-x: auto;
            margin: 12px 0;
            font-size: 13px;
            line-height: 1.5;
        }

        code {
            font-family: "JetBrains Mono", "Fira Code", "Cascadia Code", monospace;
        }

        :not(pre) > code {
            background: #f1f5f9;
            padding: 2px 6px;
            border-radius: 4px;
            font-size: 13px;
        }

        /* Mermaid diagrams */
        .mermaid {
            margin: 20px auto;
            text-align: center;
        }

        .diagram-container {
            background: var(--card-bg);
            border: 1px solid var(--border);
            border-radius: 8px;
            padding: 24px;
            margin: 16px 0;
            overflow-x: auto;
        }

        .diagram-container .diagram-title {
            font-size: 14px;
            font-weight: 600;
            color: var(--text-secondary);
            margin-bottom: 12px;
        }

        /* Blockquote / Callout */
        .callout {
            padding: 12px 16px;
            border-radius: 8px;
            margin: 12px 0;
            font-size: 14px;
        }

        .callout-info {
            background: var(--info-light);
            border-left: 4px solid var(--info);
        }

        .callout-warning {
            background: var(--warning-light);
            border-left: 4px solid var(--warning);
        }

        .callout-danger {
            background: var(--danger-light);
            border-left: 4px solid var(--danger);
        }

        .callout-success {
            background: var(--success-light);
            border-left: 4px solid var(--success);
        }

        /* Responsive */
        @media (max-width: 768px) {
            nav { transform: translateX(-100%); }
            nav.open { transform: translateX(0); }
            .menu-toggle { display: block; }
            main { margin-left: 0; padding: 60px 16px 24px; }
        }

        /* Print */
        @media print {
            nav, .menu-toggle { display: none !important; }
            main { margin-left: 0; padding: 0; max-width: 100%; }
            .card { break-inside: avoid; }
            body { background: #fff; }
        }
    </style>
</head>
<body>
    <button class="menu-toggle" onclick="document.querySelector('nav').classList.toggle('open')">&#9776;</button>

    <nav>
        <div class="nav-header">
            <h2>{project_name}</h2>
            <div class="version">{document_type} | {date}</div>
        </div>
        <!-- Auto-generated section links -->
    </nav>

    <main>
        <div class="page-header">
            <h1>{document_title}</h1>
            <div class="meta">
                项目：{project_name} | 版本：v{version} | 日期：{date} | 角色：{role_name}
            </div>
        </div>

        <!-- Content sections go here -->
        <!-- Each section: <section id="section-slug"><h2>Section Title</h2>...</section> -->

    </main>

    <script>
        // Mermaid initialization
        mermaid.initialize({
            startOnLoad: true,
            theme: 'default',
            flowchart: { useMaxWidth: true, htmlLabels: true, curve: 'basis' },
            er: { useMaxWidth: true }
        });

        // Auto-generate sidebar navigation from h2 headings
        document.addEventListener('DOMContentLoaded', function() {
            var nav = document.querySelector('nav');
            var main = document.querySelector('main');
            var headings = main.querySelectorAll('section > h2');
            headings.forEach(function(h2) {
                var section = h2.parentElement;
                var id = section.id || h2.textContent.toLowerCase()
                    .replace(/[^a-z0-9一-龥]+/g, '-')
                    .replace(/^-|-$/g, '');
                section.id = id;
                var link = document.createElement('a');
                link.href = '#' + id;
                link.textContent = h2.textContent;
                nav.appendChild(link);
            });

            // Active link tracking on scroll
            var links = nav.querySelectorAll('a');
            window.addEventListener('scroll', function() {
                var scrollTop = window.scrollY;
                links.forEach(function(link) {
                    var target = document.querySelector(link.getAttribute('href'));
                    if (target && target.offsetTop - 100 <= scrollTop) {
                        links.forEach(function(l) { l.classList.remove('active'); });
                        link.classList.add('active');
                    }
                });
            });
        });
    </script>
</body>
</html>
```

## Document-Specific Mermaid Examples

### PRD (prd.html)

**Business Flow Diagram**:
```html
<div class="diagram-container">
    <div class="diagram-title">业务流程图</div>
    <div class="mermaid">
graph TD
    A[用户访问] --> B{已注册?}
    B -->|是| C[登录]
    B -->|否| D[注册]
    D --> C
    C --> E[首页]
    E --> F[浏览商品]
    F --> G[加入购物车]
    G --> H[下单]
    H --> I[支付]
    I --> J{支付成功?}
    J -->|是| K[订单完成]
    J -->|否| L[重试]
    L --> I
    </div>
</div>
```

**Feature Priority Matrix** (rendered as styled HTML table with colored badges):
```html
<table>
    <thead><tr><th>功能</th><th>优先级</th><th>状态</th></tr></thead>
    <tbody>
        <tr><td>用户注册登录</td><td><span class="badge badge-critical">Must Have</span></td><td>已规划</td></tr>
        <tr><td>商品管理</td><td><span class="badge badge-high">Should Have</span></td><td>已规划</td></tr>
        <tr><td>数据导出</td><td><span class="badge badge-skip">Won't Have</span></td><td>下版本</td></tr>
    </tbody>
</table>
```

### UI Design (ui-design.html)

**Page Navigation Flow**:
```html
<div class="diagram-container">
    <div class="diagram-title">页面导航流</div>
    <div class="mermaid">
graph LR
    Login[登录页] --> Home[首页]
    Home --> List[列表页]
    Home --> Profile[个人中心]
    List --> Detail[详情页]
    Detail --> Edit[编辑页]
    Profile --> Settings[设置页]
    </div>
</div>
```

### Architecture (architecture.html)

**System Architecture Diagram**:
```html
<div class="diagram-container">
    <div class="diagram-title">系统架构图</div>
    <div class="mermaid">
graph TB
    subgraph Frontend [前端]
        SPA[SPA 应用]
    end
    subgraph Backend [后端]
        Gateway[API 网关]
        Auth[认证模块]
        Biz[业务模块]
        Cache[缓存层]
    end
    subgraph Data [数据层]
        DB[(数据库)]
        FS[文件存储]
    end
    SPA --> Gateway
    Gateway --> Auth
    Gateway --> Biz
    Biz --> Cache
    Biz --> DB
    Biz --> FS
    </div>
</div>
```

**Module Dependency Graph**:
```html
<div class="diagram-container">
    <div class="diagram-title">模块依赖图</div>
    <div class="mermaid">
graph TD
    User[用户模块] --> Auth[认证模块]
    Order[订单模块] --> User
    Order --> Product[商品模块]
    Cart[购物车模块] --> User
    Cart --> Product
    Payment[支付模块] --> Order
    </div>
</div>
```

### Database (database.html)

**Entity Relationship Diagram**:
```html
<div class="diagram-container">
    <div class="diagram-title">ER 实体关系图</div>
    <div class="mermaid">
erDiagram
    USER ||--o{ ORDER : creates
    USER ||--o{ CART : has
    PRODUCT ||--o{ ORDER_ITEM : included_in
    ORDER ||--|{ ORDER_ITEM : contains
    CATEGORY ||--o{ PRODUCT : has

    USER {
        int id PK
        string username
        string email
        string password_hash
        datetime created_at
    }
    PRODUCT {
        int id PK
        string name
        decimal price
        int stock
        int category_id FK
    }
    ORDER {
        int id PK
        int user_id FK
        decimal total
        string status
        datetime created_at
    }
    ORDER_ITEM {
        int id PK
        int order_id FK
        int product_id FK
        int quantity
        decimal unit_price
    }
    CATEGORY {
        int id PK
        string name
        int parent_id FK
    }
    CART {
        int id PK
        int user_id FK
        int product_id FK
        int quantity
    }
    </div>
</div>
```

### Code Review (code-review.html)

**Review Summary Stats**:
```html
<div class="stats-grid">
    <div class="stat-card">
        <div class="stat-value">92%</div>
        <div class="stat-label">架构一致性</div>
    </div>
    <div class="stat-card">
        <div class="stat-value success">45/47</div>
        <div class="stat-label">API 契约匹配</div>
    </div>
    <div class="stat-card">
        <div class="stat-value danger">3</div>
        <div class="stat-label">Critical Issues</div>
    </div>
    <div class="stat-card">
        <div class="stat-value warning">7</div>
        <div class="stat-label">Recommendations</div>
    </div>
</div>
```

### Test Report (test-report.html)

**Bug Severity Distribution**:
```html
<div class="diagram-container">
    <div class="diagram-title">Bug 严重度分布</div>
    <div class="mermaid">
pie title Bug 严重度分布
    "Critical" : 2
    "High" : 5
    "Medium" : 8
    "Low" : 12
    "Info" : 3
    </div>
</div>
```

**Test Execution Summary**:
```html
<div class="stats-grid">
    <div class="stat-card">
        <div class="stat-value">42</div>
        <div class="stat-label">测试用例总数</div>
    </div>
    <div class="stat-card">
        <div class="stat-value success">35</div>
        <div class="stat-label">通过</div>
    </div>
    <div class="stat-card">
        <div class="stat-value danger">5</div>
        <div class="stat-label">失败</div>
    </div>
    <div class="stat-card">
        <div class="stat-value warning">2</div>
        <div class="stat-label">阻塞</div>
    </div>
</div>
```

**Bug Table with Status Badges**:
```html
<table>
    <thead>
        <tr><th>Bug ID</th><th>标题</th><th>严重度</th><th>责任方</th><th>状态</th></tr>
    </thead>
    <tbody>
        <tr>
            <td>BUG-001</td>
            <td>登录接口返回 500</td>
            <td><span class="badge badge-critical">Critical</span></td>
            <td>Backend</td>
            <td><span class="badge badge-pass">已修复</span></td>
        </tr>
        <tr>
            <td>BUG-002</td>
            <td>列表分页不生效</td>
            <td><span class="badge badge-high">High</span></td>
            <td>Frontend</td>
            <td><span class="badge badge-pending">待修复</span></td>
        </tr>
    </tbody>
</table>
```

### Security Audit (security-audit.html)

**Attack Surface Map**:
```html
<div class="diagram-container">
    <div class="diagram-title">攻击面映射</div>
    <div class="mermaid">
graph LR
    Internet((互联网)) --> WAF[Web 应用防火墙]
    WAF --> API[API 网关]
    API --> Auth[认证服务]
    API --> Biz[业务服务]
    Auth --> DB[(数据库)]
    Biz --> DB
    Biz --> Cache[(缓存)]
    style WAF fill:#16a34a,color:#fff
    style API fill:#2563eb,color:#fff
    style Auth fill:#d97706,color:#fff
    </div>
</div>
```

### UAT (uat.html)

**Feedback Processing Flow**:
```html
<div class="diagram-container">
    <div class="diagram-title">反馈处理流程</div>
    <div class="mermaid">
graph TD
    Trial[用户试用] --> Feedback[收集反馈]
    Feedback --> Classify{分类}
    Classify -->|Bug| BugFix[Bug 修复]
    Classify -->|UX问题| UXFix[UX 调整]
    Classify -->|功能建议| Backlog[加入 Backlog]
    BugFix --> Verify[回归验证]
    UXFix --> Verify
    Verify --> Signoff[用户签收]
    Backlog --> Signoff
    </div>
</div>
```

## Status Color Coding Guide

| Status | CSS Class | Color | Usage |
|--------|-----------|-------|-------|
| Pass / Complete / Yes | `badge-pass` | Green | Test results, completed items |
| Fail / Critical / Blocking | `badge-fail` | Red | Bugs, errors, blocking issues |
| Warning / Medium / Conditional | `badge-warn` | Amber | Medium bugs, conditional pass |
| Info / Low / Pending | `badge-info` | Cyan/Blue | Info findings, pending items |
| Skip / N/A | `badge-skip` | Gray | Skipped items |
| High Priority | `badge-high` | Orange | High priority items |

## Markdown-to-HTML Conversion Guide

When converting from Markdown to HTML:

| Markdown | HTML |
|----------|------|
| `## Title` | `<section id="title-slug"><h2>Title</h2>...</section>` |
| Markdown table | `<table><thead>...<tbody>...` with optional badge classes on status cells |
| Bullet list | `<ul><li>...</li></ul>` |
| Numbered list | `<ol><li>...</li></ol>` |
| ` ```mermaid ` code block | `<div class="mermaid">...</div>` inside a `.diagram-container` |
| Internal link `[text](#id)` | `<a href="#section-id">text</a>` |
| Bold status text | `<span class="badge badge-xxx">text</span>` |
| Stats/numbers | `<div class="stats-grid"><div class="stat-card">...` |

## Directory Handling

Before writing the first HTML report in a project:
1. Create `.doc/` directory
2. Create `.doc/html_report/` directory
3. Place a simple `index.html` as navigation hub (optional, links to all reports)

The `.doc/html_report/` directory is separate from `docs/` (Markdown) and `.spec/` (AI specs).
It serves as the human-facing visual output layer.
