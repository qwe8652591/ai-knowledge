# Official Platform Capabilities

本文件只记录官方能力和可以作为 harness 原语的部分。OpenAI Codex 官方手册 helper 在本机抓取时返回 403，因此 Codex 部分主要使用本地已克隆的官方 `openai/codex-action` 源码、`openai/codex` 本地源码中的文档链接，以及官方公开 URL 作为来源。

## OpenAI Codex

### Codex GitHub Action

官方仓库：[`openai/codex-action`](https://github.com/openai/codex-action)

本地源码：`repos/software-development-harness/openai-codex-action`

它提供的是一个 CI/PR harness 原语：在 GitHub Actions workflow 中运行 `codex exec`，并把 Codex 的最终输出作为 action output。

关键能力：

- 安装 Codex CLI 和 Responses API proxy。
- 支持 inline prompt 或 `prompt-file`。
- 支持 `working-directory`、`model`、`effort`、`codex-args`、`output-schema`。
- 支持 `workspace-write`、`read-only`、`danger-full-access` 等 Codex sandbox 参数。
- 额外提供 action 级 `safety-strategy`：`drop-sudo`、`unprivileged-user`、`read-only`、`unsafe`。
- 支持 `allow-users`、`allow-bots` 等触发者限制。
- README 直接给出 PR review workflow 示例：checkout PR merge commit、fetch base/head、把 PR 信息放入 prompt、运行 Codex、把 final message 发回 PR comment。

为什么这样设计：

- GitHub Actions 是标准交付入口，天然有 event、permissions、secrets、日志和审计。
- Action 不把 Codex 做成“全自动合并者”，而是把 Codex 输出交给 workflow 后续步骤处理，保持组合性。
- 安全策略放在 action 层，是因为 CI runner 的 sudo、secrets、untrusted PR input 风险不完全属于 Codex CLI 自身。

边界：

- 它是低层积木，不是完整 SDLC 工具；任务拆分、看板、长期记忆、review policy 需要外层组合。
- 对 untrusted PR/issue 内容要高度谨慎；仓库内 `AGENTS.md` 等 instruction 文件在 PR-controlled content 中也可能成为输入攻击面。

### Codex Repo Instructions, Skills, Hooks, Config

相关官方公开入口：

- [`AGENTS.md` guide](https://developers.openai.com/codex/guides/agents-md)
- [`Codex skills`](https://developers.openai.com/codex/skills)
- [`Codex security / sandboxing`](https://developers.openai.com/codex/security)
- [`openai/codex` repository](https://github.com/openai/codex)

可作为开发层 harness 的原语：

- `AGENTS.md`：把 repo 约定、测试命令、review 规则、目录特例沉淀到版本控制。
- Skills：把可复用开发流程打包，而不是每次复制 prompt。
- Hooks/config：把生命周期和工具调用周边的检查做成可执行约束。
- MCP：把受控外部能力暴露给 Codex，避免直接给全权限 shell 或 secrets。

设计含义：

Codex 本身偏执行器和交互式 coding agent。软件开发层 harness 可以在它外面组合：

`AGENTS.md + skills + hooks + GitHub Action + CI gates + worktree manager`

## Anthropic Claude Code

官方入口：

- [Claude Code GitHub Actions](https://docs.anthropic.com/en/docs/claude-code/github-actions)
- [Hooks reference](https://docs.anthropic.com/en/docs/claude-code/hooks)
- [Create custom subagents](https://docs.anthropic.com/en/docs/claude-code/sub-agents)
- [Skills](https://docs.anthropic.com/en/docs/claude-code/skills)
- [Plugins](https://docs.anthropic.com/en/docs/claude-code/plugins)
- [`anthropics/claude-code-action`](https://github.com/anthropics/claude-code-action)

### Claude Code Action

本地源码：`repos/software-development-harness/anthropic-claude-code-action`

它比 `openai/codex-action` 更接近“GitHub 协作 bot harness”：

- 支持 `@claude` trigger phrase。
- 支持 issue/PR assignment、label trigger、automation prompt。
- 支持 PR/issue comments、PR reviews、code implementation。
- 支持 progress tracking、structured outputs、branch naming、commit signing。
- 支持 Anthropic API key、Claude Code OAuth、AWS Bedrock、Google Vertex AI、Microsoft Foundry。
- 支持安装 Claude Code plugins 和 plugin marketplaces。

为什么这样设计：

- Claude Code Action 直接围绕 GitHub issue/PR 协作模型设计，所以内建 trigger、branch、comment、progress、structured output。
- 它的模式检测让同一个 action 可以覆盖问答、review、实现、自动化，不需要为每类 workflow 写独立 action。
- 支持多云 provider，说明企业落地时身份、合规和供应商环境是第一等问题。

### Claude Code Hooks / Skills / Plugins / Subagents

这些能力是软件开发层 harness 的关键原语：

- Hooks：在生命周期点运行命令、HTTP 或 MCP handler，可用于测试门、lint、secret scan、policy check、session bootstrap。
- Skills：把工作流封装为可触发的知识和步骤。
- Plugins：把 skills、commands、hooks、MCP 等打包发布。
- Subagents：把 review、test、security、architecture 等角色拆成可复用执行单元。
- Worktrees：把并行任务隔离到独立 Git 工作区，降低文件冲突和上下文串扰。

## GitHub Agent HQ / Third-Party Agents

官方入口：

- [About third-party coding agents - GitHub Docs](https://docs.github.com/copilot/concepts/agents/about-third-party-agents)
- [Pick your agent: Use Claude and Codex on Agent HQ - GitHub Blog](https://github.blog/news-insights/company-news/pick-your-agent-use-claude-and-codex-on-agent-hq/)
- [Introducing Agent HQ - GitHub Blog](https://github.blog/news-insights/company-news/welcome-home-agents/)

GitHub 的方向很明确：让 GitHub 变成多 agent 的任务控制面。GitHub blog 在 2026-02-04 公布 Claude 和 OpenAI Codex 在 Agent HQ 中 public preview，可在 GitHub/VS Code 中使用，面向 Copilot Pro+ 或 Enterprise 订阅。

这对软件开发层 harness 的意义：

- issue、PR、code review、enterprise policy、metrics 会成为 agent 的自然控制平面。
- “选择不同 agent 执行不同步骤”将变成平台能力，而不是每个团队自己写 glue code。
- 自建 harness 更应该关注 repo-specific policy、任务模型、证据、质量门，而不是重复造 agent marketplace。

## Official Primitives Summary

| 平台 | 已有原语 | 适合做什么 | 缺口 |
| --- | --- | --- | --- |
| OpenAI Codex | `codex exec`、GitHub Action、`AGENTS.md`、skills、hooks、sandbox、MCP | CI/PR bot、repo convention、受控执行 | 外层任务/看板/长期项目状态需要自己组合 |
| Claude Code | GitHub Action、hooks、skills、plugins、subagents、worktrees、MCP | GitHub 协作、流程注入、角色化 review/实现 | 团队级任务数据库和跨项目治理仍需外层 |
| GitHub Agent HQ | third-party agents、issue/PR 工作流、GitHub/VS Code surface | 多 agent 分配、跟踪、治理 | 当前更像平台入口，细粒度工程流程仍需项目层设计 |
