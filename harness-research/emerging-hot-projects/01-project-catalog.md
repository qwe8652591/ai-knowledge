# 项目目录

本轮选择标准：用户点名 OpenClaw、Hermes Agent；另外补充近期高热度或与 Claude Code / Codex / OpenClaw / Hermes 相关的 agent harness、coding harness、sandbox harness 项目。

## 项目清单

| 项目 | 本地源码 | 主要定位 | Harness 类型 | 本轮判断 |
| --- | --- | --- | --- | --- |
| openclaw/openclaw | `repos/emerging-hot-projects/openclaw` | 本地优先个人 AI assistant 和 gateway | Agent runtime harness | 是，核心就是 gateway/session/tool/plugin/sandbox 控制面 |
| goldmar/openclaw-code-agent | `repos/emerging-hot-projects/openclaw-code-agent` | OpenClaw 插件，托管 Claude Code/Codex/OpenCode 后台 coding session | 软件开发层 harness | 是，强 harness：plan approval、worktree、merge/PR、goal loop |
| NousResearch/hermes-agent | `repos/emerging-hot-projects/hermes-agent` | 自改进个人 agent，记忆、技能、消息网关、终端后端 | Agent runtime harness | 是，偏长期运行和学习闭环 |
| NVIDIA/NemoClaw | `repos/emerging-hot-projects/nemo-claw` | 在 OpenShell 中更安全地运行 OpenClaw/Hermes | 安全/环境 harness | 是，sandbox lifecycle、network policy、inference routing |
| NVIDIA/OpenShell | `repos/emerging-hot-projects/openshell` | autonomous agent 的安全私有 runtime | 安全/环境 harness | 是，agent-agnostic sandbox/policy harness |
| TheAiSingularity/hermesclaw | `repos/emerging-hot-projects/hermesclaw` | Hermes Agent + OpenShell 的社区打包 | 集成型安全 harness | 是，但更像集成发行版 |
| johannesjo/parallel-code | `repos/emerging-hot-projects/parallel-code` | 多 coding agent 并行 GUI，每任务独立 git worktree | 软件开发层 harness | 是，任务/worktree/diff/merge control plane |
| sipyourdrink-ltd/bernstein | `repos/emerging-hot-projects/bernstein` | 确定性多 coding-agent 调度器，审计链和 lineage | 软件开发治理 harness | 是，强治理/合规导向 |
| nwiizo/ccswarm | `repos/emerging-hot-projects/ccswarm` | Rust workflow engine，驱动 Claude/Codex 执行 plan-review-fix | 软件开发流程 harness | 是，YAML flow、provider adapter、NDJSON audit |
| truffle-ai/dexto | `repos/emerging-hot-projects/dexto` | 通用 agent harness，附带 coding agent | Agent runtime + coding harness | 是，README 直接定义为 agent harness |
| Enderfga/claw-orchestrator | `repos/emerging-hot-projects/claw-orchestrator` | 把 Claude Code/Codex/Gemini/Cursor/OpenCode 包成持久 session runtime | 软件开发层 harness | 是，多引擎 session、council、autoloop、MCP/OpenClaw plugin |
| jnMetaCode/agency-agents-zh | `repos/emerging-hot-projects/agency-agents-zh` | 中文专家角色/agent 包和编排器生态 | 内容层/角色层 | 间接，不是核心 harness |
| VoltAgent/awesome-openclaw-skills | `repos/emerging-hot-projects/awesome-openclaw-skills` | OpenClaw skills 索引 | 生态目录 | 不是 harness 实现，但说明 skills/registry/permission/audit 需求 |
| mergisi/awesome-openclaw-agents | `repos/emerging-hot-projects/awesome-openclaw-agents` | OpenClaw agent/persona 模板索引 | 生态目录 | 不是 harness 实现，但可作为 agent 配置样本库 |

## 重点分组

### 1. OpenClaw/Hermes 主线

OpenClaw 和 Hermes Agent 是两个完整 runtime harness。它们都处理：

- 多 session 和会话状态；
- tool/plugin/skill 调用；
- 消息或 CLI/TUI 入口；
- 模型/provider 抽象；
- 本地文件、终端、浏览器、MCP 等工具能力；
- 权限、sandbox 或人工确认。

差异是 OpenClaw 更像本地 gateway 和多渠道控制面，Hermes 更像长期个人 agent 和学习/记忆系统。

### 2. OpenShell/NemoClaw 安全主线

OpenShell 是环境级 harness：agent 不一定知道自己被包住，但 filesystem、network、process、inference 都被 policy 管。NemoClaw 则把 OpenClaw/Hermes 放进这个环境，并补上 onboarding、blueprint、policy、inference routing、lifecycle。

这条线的价值是安全边界独立于模型和 agent prompt。

### 3. Claude Code/Codex 软件开发 harness 主线

这些项目都承认 Claude Code、Codex、Gemini CLI、OpenCode 等已有强能力，自己不重造模型，也不重造 coding agent，而是补工程闭环：

- 任务和计划；
- 独立 worktree/branch；
- 多 agent 并行；
- plan approval；
- review/fix loop；
- merge/PR；
- CI/test/quality gate；
- audit/log/replay；
- cost/status/notification。

最典型的是 `openclaw-code-agent`、`parallel-code`、`bernstein`、`ccswarm`、`claw-orchestrator`。

## 下载状态

所有仓库已 clone 到 `repos/emerging-hot-projects/`。其中 `bernstein` 在 Windows checkout 时触发了部分长路径错误，但核心 README、docs、src、tests、eval 等已可读；详见 [source-repos.md](source-repos.md)。

