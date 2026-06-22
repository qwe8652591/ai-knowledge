# Cross-Project Comparison

## Best By Use Case

| 你要解决的问题 | 首选 | 备选 | 原因 |
| --- | --- | --- | --- |
| 在 GitHub Actions 里受控运行 Codex | `openai/codex-action` | 自己写 workflow 调 `codex exec` | 官方 action 已处理 CLI 安装、proxy、sandbox、触发者限制 |
| 在 GitHub issue/PR 中让 Claude 参与 review/实现 | `anthropics/claude-code-action` | GitHub Agent HQ | 官方 action 的 trigger、branch、progress、structured output 更完整 |
| 本地并行跑多个 Claude/Codex 任务 | Claude Squad | Vibe Kanban | Claude Squad 简单、tmux + worktree；Vibe Kanban 更完整但更重 |
| 需要 UI 化任务板和 preview/review | Vibe Kanban | Nimbalyst | Vibe Kanban 明确支持多 agent、workspace、dev server、diff review、PR |
| 需求/PRD 到任务图 | Task Master AI | BMAD Method | Task Master 更专注任务数据库和 MCP/CLI |
| 完整 SDLC 方法论 | BMAD Method | Superpowers | BMAD 更像端到端 agile AI development framework |
| 跨多 agent 的工程纪律技能包 | Superpowers | SuperClaude | Superpowers 明确支持 Claude Code、Codex App/CLI、Cursor、Gemini 等 |
| Claude Code 结构化命令/角色平台 | SuperClaude | Ruflo | SuperClaude 较聚焦 slash commands、agents、modes、MCP |
| 多 agent/swarm/memory 研究 | Ruflo | OpenHands/AutoGen | Ruflo 覆盖 Claude Code 和 Codex，但复杂度最高 |
| 独立 PR review/describe/improve | PR-Agent | Codex/Claude Action review prompt | PR-Agent 的 PR adapter 和 compression 成熟 |

## Project-Level Pros, Cons, And Design Reasons

### OpenAI Codex Action

优点：

- 官方低层 Action，组合性强。
- 输入/输出清楚，适合做自定义 PR bot 或 CI agent step。
- 明确暴露 sandbox 和 safety strategy，安全边界意识强。

缺点：

- 不提供任务板、状态机、长期项目流程。
- PR/issue 触发逻辑需要 workflow 自己写。
- 对外部 contributor 场景仍需用户自己设计权限和 prompt injection 防护。

为什么这样设计：

- OpenAI 把它做成 `codex exec` 的安全 wrapper，而不是完整产品。这样适配面更广，也避免替用户决定 SDLC 流程。

### Claude Code Action

优点：

- GitHub 协作语义更完整：`@claude`、assignment、label、branch、progress、structured output。
- 支持 review、问答、实现、自动化等模式。
- 支持多 provider 和 Claude Code plugin 安装。

缺点：

- 功能面更大，配置和安全策略也更复杂。
- 依赖 GitHub 事件模型，迁移到非 GitHub 平台需要另建适配。

为什么这样设计：

- Anthropic 把 Claude Code 直接带进 issue/PR conversation，所以 action 要理解 GitHub 上下文，而不只是运行一条 CLI 命令。

### Vibe Kanban

优点：

- 把开发者的新工作方式定义得很清楚：planning and review coding agents。
- workspace 设计完整：branch、terminal、dev server、diff review、browser preview、PR creation。
- 支持 Claude Code、Codex、Gemini CLI、Copilot、Cursor、OpenCode 等多个 agent。

缺点：

- README 显示 Vibe Kanban 正在 sunsetting，需要关注后续维护。
- 系统比 terminal manager 重，部署和本地服务复杂度更高。

为什么这样设计：

- 它假设人类瓶颈已经从写代码转向规划和审查，所以产品形态是看板 + workspace + review，而不是单纯 terminal。

### Claude Squad

优点：

- 极简而有效：tmux + git worktrees + TUI。
- 多个 agent session 同时跑，每个任务独立 workspace。
- 支持 Claude Code、Codex、Gemini、Aider 等。

缺点：

- 缺少完整的需求/任务数据库和团队协作 UI。
- 依赖 tmux 和本地环境，Windows 原生体验可能受限。

为什么这样设计：

- 它把问题收缩到“如何管理多个本地 coding agents”，所以直接使用成熟 Unix/Git 原语，不做重平台。

### Crystal / Nimbalyst

优点：

- 方向有价值：AI-native workspace、实时编辑、multi-editor、worktree isolation。
- 覆盖代码、Markdown、表格、图表、视觉文档等更宽开发资产。

缺点：

- Crystal 已 deprecated，README 推荐迁移 Nimbalyst；当前仓库更多是历史参考。
- 不是轻量 harness，偏完整桌面产品。

为什么这样设计：

- 它认为 coding agent 协作不只发生在终端，也发生在编辑器、文档、图表和数据文件中。

### Task Master AI

优点：

- 任务状态明确：PRD、tasks、subtasks、dependencies、next task。
- 支持 MCP/CLI，能接 Claude Code、Codex CLI、Cursor 等。
- 很适合长项目减少需求漂移。

缺点：

- 不直接保证代码质量；仍需要 worktree、CI、review gate。
- 任务拆得好不好仍取决于输入 PRD 和 human review。

为什么这样设计：

- 需求和任务是 AI coding 的稳定锚点；把它们文件化/结构化，比不断重讲上下文更可靠。

### BMAD Method

优点：

- 覆盖完整生命周期，角色和 workflows 丰富。
- 支持不同复杂度项目，强调 scale-adaptive planning。
- 适合团队方法论和规范化交付。

缺点：

- 初期学习和安装成本较高。
- 对小修小改可能太重。

为什么这样设计：

- BMAD 假设 AI development 的关键不是“让 agent 更自由”，而是给 agent 和人类一个共同的 agile process。

### Superpowers

优点：

- 明确是“coding agents 的软件开发方法论”，并跨 Claude Code、Codex App/CLI、Cursor、Gemini 等。
- 工作流很工程化：brainstorming、worktrees、plans、TDD、subagent review、finish branch。
- 强调 evidence over claims，适合直接抽取成我们自己的最小流程。

缺点：

- 对团队既有流程会有侵入，需要适配。
- 强制 TDD 等规则对探索性任务可能显重。

为什么这样设计：

- 它把 agent 容易犯的错误视为工程流程问题：跳过需求澄清、跳过测试、缺少 review、不会收尾。因此用技能和 session-start 行为来强制流程。

### SuperClaude

优点：

- Claude Code 生态内成熟的 commands/agents/modes/MCP 包装。
- 覆盖 research、brainstorm、implement、test、project management 等开发命令。
- 安装和使用相对直接。

缺点：

- 主要围绕 Claude Code，跨 agent 不如 Superpowers。
- slash command 很多，团队要治理哪些命令允许使用。

为什么这样设计：

- Claude Code 的可扩展面天然是 slash command、agent、MCP，所以它选择把结构化开发能力注入这些表面。

### Ruflo / Claude Flow

优点：

- 覆盖最广：Claude Code、Codex adapter、MCP、memory、swarm、hooks、plugins、federation。
- 对“multi-agent coding system”研究价值高。
- 有明确 Codex adapter 思路：Codex 执行，Claude Flow/Ruflo 负责协调和学习。

缺点：

- 复杂度最高，可能超出普通工程团队需要。
- 大量插件和宣称需要逐项源码验证，不能直接按 README 当生产事实。

为什么这样设计：

- 它追求的是 agent control plane，而不是单一开发流程；因此引入 memory、swarm、federation、plugin marketplace。

### PR-Agent

优点：

- PR 边界清晰，功能成熟：describe、review、improve、ask。
- 支持多个 Git provider。
- PR compression 和 dynamic context 对大 diff 很有价值。

缺点：

- 不负责需求、实现和 worktree。
- 不是 Claude Code/Codex 专属；需要和 coding agent harness 组合。

为什么这样设计：

- PR review 是软件交付中最稳定的接口之一。把 harness 边界收窄到 PR，可以做得更可靠、更可移植。

## Design Tradeoffs

### Light vs Heavy

轻量工具如 Claude Squad、Codex Action 容易嵌入现有流程，但要自己补任务状态和 review policy。重型工具如 Vibe Kanban、BMAD、Ruflo 提供完整体验，但引入新的流程和维护面。

### Local-first vs GitHub-first

Local-first 适合个人和小团队高速迭代，GitHub-first 适合团队审计、权限、CI 和外部协作。生产环境最好两者结合：本地 worktree 执行，GitHub PR/CI 做最终门。

### Methodology vs Mechanism

Task Master/BMAD/Superpowers 解决“做什么、按什么流程做”；Codex/Claude Actions、Claude Squad、Vibe Kanban 解决“在哪里执行、如何隔离和 review”。两类必须组合才完整。

### Agent Freedom vs Deterministic Gates

越是给 agent 自由，越需要确定性 gate：tests、lint、CI、security scan、human approval、PR review。优秀 harness 不靠模型自觉，而是靠流程把风险收口。

## Recommendation For Our Research Direction

如果我们要做自己的软件开发层 harness，建议先走小而硬的路线：

1. 任务层参考 Task Master：`spec -> tasks -> next task -> status`。
2. 执行层参考 Claude Squad：每任务独立 worktree，可选择 Claude Code 或 Codex。
3. 过程层参考 Superpowers：必须先批准 spec/plan，必须跑验证，必须 review diff。
4. CI 层参考 Codex Action/Claude Code Action：把 review 和小修复接进 GitHub。
5. PR 层参考 PR-Agent：只审 diff，压缩上下文，输出可操作 comments。
6. 方法论层按需接 BMAD：大项目才启用完整角色/workflow。

这条路线避免一开始就做 multi-agent swarm，同时保留未来扩展到 Ruflo/SuperClaude/Agent HQ 的接口。
