# Project Catalog

本轮下载了 11 个软件开发层 harness/相关项目源码，放在 `repos/software-development-harness/`。这些项目不是同一类东西，所以先按控制层分类。

## Downloaded Projects

| 项目 | 本地目录 | 目标 agent/平台 | 类别 | 核心思想 |
| --- | --- | --- | --- | --- |
| `openai/codex-action` | `openai-codex-action` | Codex CLI + GitHub Actions | CI/PR harness | 在 GitHub workflow 中安全运行 `codex exec` |
| `anthropics/claude-code-action` | `anthropic-claude-code-action` | Claude Code + GitHub | CI/PR/issue harness | 用 `@claude`、issue、PR、label、prompt 驱动 Claude Code |
| `BloopAI/vibe-kanban` | `bloop-vibe-kanban` | Claude Code、Codex、Gemini CLI、Copilot 等 | Kanban/workspace harness | 以看板规划任务，每个 workspace 有 branch、terminal、dev server、diff review |
| `smtg-ai/claude-squad` | `smtg-claude-squad` | Claude Code、Codex、Gemini、Aider 等 | Terminal/worktree harness | TUI 管理多个 agent session，每个任务一个 isolated git workspace |
| `stravu/crystal` | `stravu-crystal` | Claude Code/Codex 类 agent | Desktop/workspace harness | Crystal 已 deprecated，迁移到 Nimbalyst；强调 editor + worktree + AI session |
| `eyaltoledano/claude-task-master` | `task-master-ai` | Claude Code、Codex CLI、Cursor、Windsurf、VS Code MCP | Requirement/task harness | PRD -> task graph -> next task -> implementation loop |
| `bmad-code-org/BMAD-METHOD` | `bmad-method` | Claude Code、Cursor 等 AI IDE/CLI | SDLC methodology harness | 角色、workflow、planning docs、skills，覆盖完整开发生命周期 |
| `SuperClaude-Org/SuperClaude_Framework` | `superclaude-framework` | Claude Code | Command/agent/method harness | 30 slash commands、20 agents、modes、MCP，把 Claude Code 变成结构化开发平台 |
| `ruvnet/ruflo` | `ruflo-claude-flow` | Claude Code、Codex | Multi-agent/plugin harness | swarms、memory、hooks、MCP、plugins、Codex adapter |
| `obra/Superpowers` | `obra-superpowers` | Claude Code、Codex App/CLI、Cursor、Gemini 等 | Cross-agent skill/method harness | 组合技能强制 brainstorming、worktrees、plans、TDD、review、finish branch |
| `qodo-ai/pr-agent` | `pr-agent` | GitHub/GitLab/Bitbucket/Azure DevOps + LLMs | PR review harness | PR describe/review/improve/ask，成熟的 diff compression 和平台适配 |

## Category Notes

### CI/PR Harness

`openai/codex-action` 和 `anthropics/claude-code-action` 是官方项目，最值得研究安全边界。

区别：

- Codex Action 更像“受控执行 `codex exec` 的低层 Action”，组合自由度高。
- Claude Code Action 更像“GitHub 协作 bot 平台”，内建 trigger、branch、progress、structured outputs、plugins。

适合借鉴：

- action inputs 如何暴露权限、sandbox、模型、prompt、输出。
- 如何限制触发者和处理不可信 PR/issue 内容。
- 如何把 final output 转成 PR comment 或 structured output。

### Worktree/Workspace Harness

Vibe Kanban、Claude Squad、Crystal/Nimbalyst 属于同一设计族。

共同点：

- 以任务为单位创建独立 workspace/worktree。
- agent 在自己的 branch/terminal 中跑。
- 人类主要做任务描述、监控、diff review、合并。

差异：

- Vibe Kanban 是完整 web/desktop-style control plane，有 kanban、dev server、browser preview、inline review、PR creation。
- Claude Squad 是更轻量的 terminal/TUI 工具，核心依赖 tmux + git worktrees。
- Crystal 已 deprecated，README 指向 Nimbalyst，方向从“多会话代码 assistant manager”扩展为“AI-native workspace”。

适合借鉴：

- 用 Git 原语隔离 agent 并行任务。
- 让用户在 diff/review/PR 层操作，而不是直接盯终端输出。

### Requirement/Task Harness

Task Master AI 的核心是把 PRD、任务、子任务、依赖和状态变成项目内的事实源。

它支持：

- MCP 方式接入编辑器/agents。
- Claude Code quick install：`claude mcp add taskmaster-ai -- npx -y task-master-ai`。
- 以 Claude Code 或 Codex CLI 作为模型/执行后端之一。
- PRD parsing、task expansion、next task、dependency、research、loop。

适合借鉴：

- 对复杂项目，先有 task graph，再让 agent 执行。
- 用 `.taskmaster/` 这类目录保存状态，避免需求只存在聊天记录。

### SDLC Methodology Harness

BMAD Method 把“软件开发过程”本身做成可安装模块。

特点：

- Scale-adaptive planning：从 bug fix 到 enterprise system 调整规划深度。
- 12+ domain experts，PM、Architect、Developer、UX 等角色。
- 34+ workflows，从 brainstorming 到 deployment。
- 支持非交互安装到 Claude Code 等工具。

适合借鉴：

- 把角色和 workflow 版本化，而不是临时 prompt。
- 让规划深度随任务复杂度变化，避免所有任务都走重流程。

### Skill/Plugin/Command Harness

Superpowers、SuperClaude、Ruflo 都围绕 skills/plugins/commands/hooks，但侧重点不同。

- Superpowers：强调工程纪律，需求澄清、worktrees、plans、TDD、review、finish branch，并且跨 Claude Code、Codex、Cursor、Gemini 等 harness。
- SuperClaude：强调 Claude Code 的 slash command、agent、mode 和 MCP 体系，像“结构化开发平台插件包”。
- Ruflo：更激进，目标是 Claude Code/Codex 的 multi-agent harness，包含 swarms、memory、federation、plugins、Codex adapter。

适合借鉴：

- 轻量技能包可以先落地工程纪律。
- 大型多 agent 框架要谨慎采用，先验证复杂度是否带来收益。

### PR Review Harness

PR-Agent 不依赖 Claude Code/Codex，但它是软件开发层 harness 的经典边界：只管 PR 质量门。

适合借鉴：

- PR diff compression。
- platform adapter：GitHub/GitLab/Bitbucket/Azure DevOps/Gitea。
- `/describe`、`/review`、`/improve`、`/ask` 这样的 PR 操作模型。
- 作为独立 review gate，而不是端到端 coding agent。

## Adjacent But Important

- GitHub Agent HQ：不是本地下载项目，但它代表平台级多 agent 控制面，Claude 和 Codex 已进入 public preview。
- OpenHands Agent Canvas / Software Agent SDK：已在上一轮研究，它们偏 agent/product runtime，可作为内层 executor 或 UI control plane 参考。
- SWE-bench / Terminal-Bench：不是软件开发流程 harness，但可以作为“任务完成质量”的外部评测来源。
