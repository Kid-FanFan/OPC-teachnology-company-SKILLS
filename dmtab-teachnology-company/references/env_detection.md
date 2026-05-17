# Environment Detection Reference

This file defines how the skill detects available tools in the user's Claude Code CLI
environment at runtime, rather than hardcoding external skill dependencies.

## Why Runtime Detection

Every user's Claude Code environment has different skills and MCP tools installed.
Hardcoding references to specific skills (e.g., `webapp-testing`) would fail for users
who don't have them. Runtime detection adapts automatically.

## Detection Mechanism

During Stage 0 initialization, after the project charter is created, fork an Environment
Detection SubAgent:

### SubAgent Task Instruction

```markdown
## Task
Scan the current Claude Code environment for available skills and MCP tools that could
assist with software development stages. Identify which ones are useful for:
- UI design and visual mockups
- Frontend code generation
- Browser-based testing
- Security code scanning
- Code quality review

## Source of Truth
The available skills and MCP tools are listed in the system context you inherit.
Read the skill names and descriptions to determine relevance.

## Output Format
Return a concise report (under 15 lines):

## 环境工具检测报告

### 可用开发工具
| 工具 | 类型 | 适用阶段 | 用途 |
|------|------|---------|------|
| {name} | Skill/MCP | Stage {N} | {one-line description} |

### 无额外工具
{If no relevant tools found, output this line instead of the table}
```

## Storing Detection Results

The detection report is stored in PROJECT_STATE.md under "Environment Capabilities":

```markdown
## Environment Capabilities
- **Detected at**: {date}
- {tool_name}: ✅ 可用（Stage {N} {usage}）
- {tool_name}: ❌ 未安装（{fallback note}）
```

This section is read by roles at their stage entry. They check it before deciding
whether to invoke external tools.

## Redetection

Environment detection only runs once during initialization. If the user installs new
skills mid-project, they can trigger redetection by:
- Using `/dmtab-teachnology-company status` and requesting a re-scan
- Or manually updating the Environment Capabilities section in PROJECT_STATE.md
