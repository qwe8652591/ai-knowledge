# Harness 判断与比较

## 判断标准

本轮把 harness 分成几个层级，避免把所有 agent 项目都混为一谈：

| 层级 | 名称 | 判断信号 |
| --- | --- | --- |
| L0 | 生态目录/内容包 | 只有 skills、roles、prompts、模板目录，本身不负责执行控制 |
| L1 | Agent runtime harness | session、tool registry、memory、permissions、MCP、gateway、agent loop |
| L2 | 软件开发层 harness | plan、worktree/branch、review、test/CI、merge/PR、coding CLI adapter |
| L3 | 安全/环境 harness | sandbox、filesystem/network/process policy、credential isolation、inference routing |
| L4 | 治理/合规 harness | deterministic scheduler、audit chain、lineage、replay、signed records、policy gates |

一个项目可以同时落在多个层级。

## 逐项目分析

### OpenClaw

判断：L1 agent runtime harness，部分 L3，借插件扩展到 L2。

OpenClaw 的核心是 local-first Gateway。它把 sessions、channels、tools、events、plugins、skills 和 multi-agent routing 放在同一个控制面里。它还提供 sandbox 模式，尤其针对非 main session 或群组/外部渠道输入做隔离。

优点：

- runtime 原语完整：gateway、session、tools、skills、channels、nodes、cron、logs。
- 本地优先，适合个人长期运行 assistant。
- 插件生态强，容易把 coding harness、安全扫描、GitHub/Slack/Gmail 等能力挂上去。

弱点：

- 默认主 session 工具可直接跑在 host 上，生产或远程暴露时必须认真配置 sandbox/DM policy。
- 范围很大，既是产品又是平台，源码深挖时需要先分清 gateway、extension、UI、SDK 的边界。

为什么这样设计：

OpenClaw 不是单个 coding agent，而是个人 assistant control plane。多渠道和长期在线决定它必须先解决身份、路由、session、工具、插件和安全默认值，再通过插件承载不同任务域。

### OpenClaw Code Agent

判断：L2 软件开发层 harness，非常明确。

它把 Claude Code、Codex、OpenCode 作为后端 harness，自己负责 chat-launched session、plan approval、session lifecycle、worktree isolation、merge/PR follow-through、goal loops、notifications、cost/status。

优点：

- 正好覆盖“OpenClaw 之上如何包 Claude Code/Codex”的问题。
- worktree lifecycle 状态很完整：active、pending decision、pr_open、merged、released、dismissed、no_change。
- 同一控制面支持 Claude Code/Codex/OpenCode，后端可换，工程流程不变。

弱点：

- 依赖 OpenClaw gateway 和本地 CLI 认证状态。
- 需要处理大量边界：计划审批、callback token、恢复、重复通知、分支清理、PR 同步。

为什么这样设计：

聊天入口很适合发起任务，但软件交付需要可观察、可中断、可合并、可回滚的状态机。所以它在 agent 后面补了一层工程执行状态机。

### Hermes Agent

判断：L1 agent runtime harness，偏记忆/技能/自改进；可间接支撑软件开发。

Hermes 的核心是自改进个人 agent。它有记忆、skills、消息网关、terminal backends、MCP、cron、subagents、trajectory 生成等能力。它不是专门的 SDLC harness，但具备通用 agent harness 的大部分组件。

优点：

- 记忆和技能自改进是强特色，适合长期个人/研究 agent。
- terminal backend 多：local、Docker、SSH、Singularity、Modal、Daytona。
- 支持 MCP、消息平台、cron 和 subagents，运行形态灵活。

弱点：

- 对“代码如何进入 main、如何过 CI、如何开 PR”没有 Bernstein/openclaw-code-agent 那么强的内建闭环。
- 自改进技能提高上限，也会带来技能质量、安全和可审计性问题。

为什么这样设计：

Hermes 追求的是会成长的长期 agent，所以把记忆、技能、用户画像、跨会话检索放在第一优先级，而不是把 Git/PR 流程作为唯一中心。

### Dexto

判断：L1 agent runtime harness，附带 coding agent，可扩展到 L2。

Dexto README 直接称自己为 agent harness：用 YAML 配置 agent，提供 session management、tool orchestration、memory、multimodal、observability、permissions、MCP、sub-agent spawning，并附带生产可用的 coding agent。

优点：

- 对“agent harness”概念表达最直接，适合做概念参考。
- 配置驱动，模型/工具/接口可替换。
- 同时提供 CLI、Web UI、REST/SSE、MCP Server、多平台入口。

弱点：

- 作为通用 harness，默认不会像 Bernstein 那样深入 Git merge gate 和审计链。
- 生态和设计目标更宽，软件开发治理能力需要额外组合。

为什么这样设计：

它想做“agent OS”，所以将工具、状态、记忆、接口和恢复抽象成通用平台，再把 coding agent 作为内置样例。

### NemoClaw

判断：L3 安全/环境 harness，面向 OpenClaw/Hermes。

NemoClaw 是在 OpenShell sandbox 内运行 OpenClaw/Hermes 的 reference stack，提供 onboarding、blueprint、sandbox lifecycle、network policy、routed inference、credential/security handling。

优点：

- 用环境隔离补 agent 安全，不依赖 prompt 自觉。
- 面向具体 agent 集成，落地性强。
- docs 和代码里有 policy、secret scanning、inference、sandbox lifecycle 等完整模块。

弱点：

- 自身不是通用 coding workflow manager。
- 依赖 OpenShell 和目标 agent 的兼容性。

为什么这样设计：

OpenClaw/Hermes 这类 always-on agent 风险来自长期运行和工具权限。NemoClaw 的设计是把它们放进可控运行环境，而不是重写 agent 本体。

### OpenShell

判断：L3 安全/环境 harness。

OpenShell 是 agent-agnostic 的 sandbox runtime。它用 gateway 管 sandbox 生命周期，用 policy engine 管 filesystem、network、process、inference，并支持 Docker、Podman、MicroVM、Kubernetes 等 compute driver。

优点：

- 边界独立于 agent，Claude Code/Codex/OpenCode/OpenClaw/Hermes 都能被包住。
- policy 是声明式 YAML，网络和 inference 可热更新。
- 把 credential provider 和 inference routing 作为一等能力，适合企业安全场景。

弱点：

- 它解决的是运行边界，不解决任务拆解、代码 review、merge 策略。
- 对个人开发者有部署和 policy 学习成本。

为什么这样设计：

真正危险的是工具执行和数据外流，所以 OpenShell 把控制点放在 OS/runtime/egress/proxy 层，降低 agent prompt 被绕过时的损害。

### HermesClaw

判断：L3 集成型安全 harness。

HermesClaw 把 Hermes Agent 放进 OpenShell/Docker，并提供 policy presets、Hermes 功能兼容表、skills library、use-case guides。

优点：

- 目标清晰：让 Hermes 的 memory/skills/gateway 在更强隔离下运行。
- policy preset 直观，便于实验 strict/gateway/permissive。

弱点：

- 更像社区集成发行版，不是底层通用框架。
- 与 NemoClaw/OpenShell/Hermes 版本兼容会成为长期维护压力。

为什么这样设计：

Hermes 用户想要完整能力，又担心技能、网络和系统调用风险。HermesClaw 选择打包和预设策略，而不是重做 runtime。

### Parallel Code

判断：L2 软件开发层 harness。

Parallel Code 是 GUI 控制面，把 Claude Code、Codex、Gemini、Copilot 等 coding CLI 分派到独立 git branch/worktree，提供 diff review、merge、task state、remote monitoring、Docker sandbox 选项。

优点：

- 工作模型非常直接：一个任务一个 worktree，一个 agent 一个隔离工作区。
- GUI 强，适合同时跑多个 coding agent 并比较结果。
- 不绑单一模型或单一 CLI。

弱点：

- 更像本地操作台，治理/审计能力不如 Bernstein。
- 需要人来判断和合并“哪个结果赢”。

为什么这样设计：

coding agent 的瓶颈经常不是单次能力，而是并行探索和结果筛选。Parallel Code 把 agent 当 worker，把人放在 review/merge 位置。

### Bernstein

判断：L2 + L4，软件开发治理 harness。

Bernstein 是确定性 Python scheduler，运行多种 CLI coding agent 到并行 git worktrees，提供 HMAC audit chain、signed agent cards、per-artifact lineage、deterministic scheduling、test/lint/type gates、merge queue、air-gap profile。

优点：

- 治理和合规能力最强，审计、lineage、replay、determinism 都是一等设计。
- 支持大量 CLI agent adapter，避免模型/工具锁定。
- 调度用 plain Python，降低“LLM 决定谁做什么”的不可复现性。

弱点：

- 对简单个人任务偏重。
- checkout 在 Windows 长路径下有部分问题，本地需启用 long paths 或在 WSL/Linux 深挖。

为什么这样设计：

它的目标用户是团队、合规敏感场景和多 agent 并行成本高的场景，因此设计重心是可复现、可审计、可验证，而不是聊天体验。

### ccswarm

判断：L2 软件开发流程 harness，带部分治理能力。

ccswarm 是 Rust workflow engine。它用 YAML flow 驱动 Claude/Codex 等 provider，执行 plan -> consensus -> implement -> review -> fix -> commit -> PR，并记录 NDJSON audit trail。

优点：

- flow 可声明、可复用，流程比临时 prompt 更稳定。
- 有 provider adapter、session bridge、events、governance、harness run 等模块。
- OK/NG 交互降低人工决策复杂度。

弱点：

- 生态和 UI 不如 OpenClaw/Parallel Code 完整。
- 对团队落地还需要围绕 repo、CI、权限做更多配置。

为什么这样设计：

它把 coding agent 当成流程执行器，重点是让每个任务走同一条可验证流程，而不是让 agent 自己临场决定流程。

### Claw Orchestrator

判断：L2 软件开发层 harness，也可作为 OpenClaw/MCP 插件。

Claw Orchestrator 把 Claude Code、Codex、Gemini、Cursor Agent、OpenCode、自定义 CLI 包成 persistent programmable sessions，并提供 council、fan-out、autoloop、ultraapp、dashboard、OpenAI-compatible proxy、MCP/OpenClaw integrations。

优点：

- 多引擎 session 抽象清晰，适合研究“如何统一包住不同 coding CLI”。
- council/fan-out/autoloop 提供多 agent 编排原语。
- OpenClaw plugin 和 MCP server 让它很容易挂到其他宿主。

弱点：

- 功能跨度大，容易形成复杂控制面。
- 与 Bernstein 相比，审计和合规不是第一卖点。

为什么这样设计：

coding CLI 原本面向人类终端。Claw Orchestrator 的核心假设是把它们变成 headless engines，再在上层组合 session、council、autoloop。

### agency-agents-zh

判断：L0 内容层，间接服务 harness。

它主要提供中文专家角色、工具适配和多角色工作流。它本身不是核心 runtime harness，但可作为 OpenClaw/Hermes/Claude Code/Cursor/Copilot 等宿主的角色和任务内容层。

优点是领域覆盖广、中文市场适配强；弱点是执行可靠性取决于宿主 harness。

### awesome-openclaw-skills / awesome-openclaw-agents

判断：L0 生态目录。

这些不是 harness 实现，但很有研究价值，因为它们展示了 OpenClaw 生态中真实的技能、agent/persona、权限、扫描、审计、注册表、安装路径和安全问题。

## 横向比较

| 目标 | 最适合项目 | 原因 |
| --- | --- | --- |
| 研究 OpenClaw 本体 harness | OpenClaw | gateway/session/tools/plugins/sandbox 是核心实现 |
| 研究 OpenClaw 上如何包 Claude Code/Codex | openclaw-code-agent | plan approval、worktree、merge/PR、goal loop 最直接 |
| 研究长期个人 agent 的记忆和技能 | Hermes Agent | memory、skills、自改进、terminal backends、gateway |
| 研究通用 agent harness 抽象 | Dexto | README 和架构直接围绕 agent harness |
| 研究安全隔离 | OpenShell + NemoClaw | policy、sandbox、credential/inference routing |
| 研究并行 coding agent 工作台 | Parallel Code | GUI + 每任务独立 worktree |
| 研究生产/合规/审计 | Bernstein | deterministic scheduler、audit chain、lineage |
| 研究流程可声明化 | ccswarm | YAML flows、provider adapter、NDJSON trace |
| 研究多 coding CLI 的统一 session API | Claw Orchestrator | persistent sessions、multi-engine、MCP/OpenClaw 插件 |

## 总体判断

这些项目绝大多数不是“用了一个叫 harness 的库”，而是各自在实现 harness 的不同切面：

- OpenClaw/Hermes/Dexto：把 LLM 包成能长期行动的 agent。
- OpenShell/NemoClaw：把 agent 放进可控执行环境。
- openclaw-code-agent/Parallel Code/Bernstein/ccswarm/Claw Orchestrator：把 coding CLI 放进真实软件工程流程。
- awesome lists/agency-agents-zh：提供技能、角色、流程模板，是 harness 生态的内容层。

所以答案是：有，而且很多。更准确地说，2026 年这一批热门项目的核心创新往往就是 harness，而不是新模型。

