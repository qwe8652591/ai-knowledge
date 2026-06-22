# Software Development Harness Research

研究日期：2026-06-22

这个目录单独研究“软件开发层面的 harness”。它不是前一轮的 agent loop / eval runner，而是包在 Claude Code、Codex、Copilot、Cursor、Gemini CLI 等 coding agents 外面的工程流程层：需求、计划、任务、worktree、CI、PR review、权限、安全、证据和交付闭环。

一句话定义：软件开发层 harness 是把 coding agent 放进真实 SDLC 的控制面，让 agent 的输出能被计划、隔离、验证、审查和合并。

## Contents

- [01-scope-and-taxonomy.md](01-scope-and-taxonomy.md)：定义这个维度，和 agent/runtime harness、evaluation harness 的边界。
- [02-official-platform-capabilities.md](02-official-platform-capabilities.md)：OpenAI Codex、Anthropic Claude Code、GitHub Agent HQ 的官方能力和可作为 harness 的原语。
- [03-project-catalog.md](03-project-catalog.md)：已下载和研究的项目目录，按 CI/PR、worktree、多会话、任务系统、方法论、技能包、PR review 分类。
- [04-patterns-and-architecture.md](04-patterns-and-architecture.md)：通用架构模式，以及我们如果自建可以采用的组合。
- [05-comparison.md](05-comparison.md)：项目横向比较、优劣、适用场景和设计原因。
- [source-repos.md](source-repos.md)：本轮新增下载的源码仓库清单、commit 和本地路径。
- [repo-download-manifest.json](repo-download-manifest.json)：机器可读的源码下载清单。
- [sources.md](sources.md)：网页和源码来源。

源码放在：

`repos/software-development-harness/`

## Quick Matrix

| 类别 | 代表项目 | 包住的对象 | 核心价值 | 主要风险 |
| --- | --- | --- | --- | --- |
| CI/PR agent action | `openai/codex-action`, `anthropics/claude-code-action` | GitHub Actions + Codex/Claude Code | 把 agent 接入 PR、issue、CI 权限模型 | prompt injection、secrets、runner 权限 |
| Worktree/task workspace | Vibe Kanban, Claude Squad, Crystal/Nimbalyst | Claude Code/Codex/Gemini 等本地 agents | 多任务并行、每任务独立 branch/worktree、可 review diffs | merge 冲突、状态分散、工具生命周期 |
| Requirement/task database | Task Master AI | PRD、任务、依赖、研究、MCP/CLI | 把需求和任务变成 repo 内状态 | 容易重流程轻验证 |
| SDLC methodology | BMAD Method | 角色、workflow、planning docs、技能 | 从 brainstorming 到部署的完整过程 | 初期学习成本高，可能过度设计 |
| Skill/plugin/process pack | Superpowers, SuperClaude, Ruflo/Claude Flow | skills、slash commands、hooks、MCP、memory | 把工程纪律变成可安装的 agent 行为 | 规则过多会淹没简单任务 |
| PR quality gate | PR-Agent | PR diff、review、describe、improve、ask | 成熟的 PR 级 review/compression 模式 | 不负责端到端实现，只做 review 边界 |

## Key Finding

这个维度确实存在，而且正在快速形成几个稳定设计方向：

1. 官方平台把“agent 进入工程流程”的入口做成 Actions、hooks、skills、plugins、subagents、worktrees、`AGENTS.md`/`CLAUDE.md` 等原语。
2. 开源工具把这些原语组合成三种产品形态：任务板、终端/worktree 管理器、方法论/技能包。
3. 最适合生产落地的路线不是“一个超级 agent”，而是“轻量 control plane + 可替换 agents + deterministic gates”。
4. 真正有价值的 harness 要保留工程证据：需求冻结、计划批准、分支隔离、测试日志、PR review、CI 结果、人工决策。

## Recommended Reading Order

1. 先读 [01-scope-and-taxonomy.md](01-scope-and-taxonomy.md)，把这个维度和前面 agent harness 分开。
2. 再读 [02-official-platform-capabilities.md](02-official-platform-capabilities.md)，理解官方已经提供哪些原语。
3. 然后读 [03-project-catalog.md](03-project-catalog.md) 和 [05-comparison.md](05-comparison.md)，决定哪些项目值得继续源码分析。
4. 最后读 [04-patterns-and-architecture.md](04-patterns-and-architecture.md)，作为自建 harness 原型的参考。
