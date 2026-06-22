# 项目目录

## 热度与下载状态

GitHub API 查询时间：2026-06-22。stars/forks 会变化，下表是本轮快照。

| 项目 | Stars | Forks | Commit | 本地状态 | 研究价值 |
| --- | ---: | ---: | --- | --- | --- |
| Yeachan-Heo/oh-my-claudecode | 36737 | 3324 | `50f6ff05` | 源码 zip 已解压 | Claude Code 多 agent/team/autopilot/Ralph 生态 |
| openai/openai-cookbook | 74304 | 12575 | `abd1e286` | archive zip 已下载，未完整解压 | 官方 cookbook，agents/evals/tracing/feedback loop 参考 |
| langchain-ai/langgraph | 35388 | 5936 | `711b3155` | 未下载，远端/文档分析 | 状态图与 durable agent loop 基础框架 |
| SWE-agent/SWE-agent | 19583 | 2140 | `abd7d697` | 未下载，远端/文档分析 | SWE issue action-observation loop 经典项目 |
| ghuntley/how-to-ralph-wiggum | 1699 | 144 | `88d488a1` | 源码 zip 已解压 | Ralph 方法论 playbook |
| cobusgreyling/loop-engineering | 599 | 78 | `cb4f4eaa` | 源码 zip 已解压 | 直接以 loop engineering 为主题的模式库和工具 |
| PageAI-Pro/ralph-loop | 260 | 36 | `6827cd85` | 源码 zip 已解压 | 可运行 Ralph Loop 脚本，Docker Sandboxes |
| liuxiaopai-ai/ralph-desktop | 184 | 17 | `178d32f0` | 源码 zip 已解压 | Ralph Loop 桌面控制器 |
| aigorahub/elves | 181 | 12 | `3af3a868` | 源码 zip 已解压 | 长任务、多批次、memory-survival loop |
| alfredolopez80/multi-agent-ralph-loop | 139 | 22 | `3191af63` | 源码 zip 已解压 | Claude Code multi-agent Ralph + memory + gates |
| agenticloops-ai/agentic-ai-engineering | 113 | 32 | `15cbe9b8` | 源码 zip 已解压 | 从 first principles 教 agent loop/evals |
| nuko-nova-dynamics/claude-goal | 1 | 0 | `4f85b303` | 源码 zip 已解压 | Claude Code Stop-hook goal loop 实现 |

## 项目类型

### Loop Engineering

`cobusgreyling/loop-engineering` 是这一轮最贴近热词本身的项目。它把 loop 拆成 primitives：automations/scheduling、worktrees、skills、plugins/connectors、sub-agents、memory/state，并提供 pattern picker、loop-audit、loop-init、loop-cost。

### Ralph 系列

Ralph 的核心是“简单外层循环 + fresh context + 持久计划文件 + 测试 backpressure”。相关项目包括：

- `how-to-ralph-wiggum`：方法论 playbook；
- `PageAI-Pro/ralph-loop`：可运行脚本，默认 Claude Code + Docker Sandboxes；
- `Ralph Desktop`：把需求澄清和 loop 执行做成桌面 UI；
- `multi-agent-ralph-loop`：把 Ralph 扩展到 Agent Teams、memory palace、quality gates；
- `Elves`：把 Ralph 扩展到夜间多批次工作、survival guide、execution log、PR review loop。

### Goal 系列

OpenAI Codex `/goal` 是官方 goal continuation loop。`claude-goal` 把类似机制迁移到 Claude Code：用 Stop hook 注入 continuation prompt，用 SQLite 保存 goal 状态，用 MCP tool 管 create/get/update/resume/abandon，用 evaluator subagent 验证完成。

### 基础 agent loop

`agentic-ai-engineering` 适合作为从零理解 loop 的教学项目：LLM call、tool use、agent loop、evaluator-optimizer、human-in-loop、eval harness。

`LangGraph` 和 `SWE-agent` 没有在本轮完整下载，但作为 loop engineering 背后的重要基础：前者强调状态图和 resilient agents，后者是 issue -> action/observation -> patch/test 的经典 SWE loop。

