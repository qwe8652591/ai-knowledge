# Emerging Hot Projects Research

研究日期：2026-06-22  
范围：补充研究 OpenClaw、Hermes Agent、OpenShell/NemoClaw，以及近期较热的 coding-agent harness / orchestration 项目，判断它们是否使用或实现 harness。

## 本目录文件

- [01-project-catalog.md](01-project-catalog.md)：项目目录、热度信号、源码下载位置。
- [02-harness-assessment.md](02-harness-assessment.md)：逐项目判断是否有 harness，以及优劣和设计原因。
- [03-source-reading-map.md](03-source-reading-map.md)：后续深入源码时优先阅读的目录和模块。
- [source-repos.md](source-repos.md)：本轮下载的仓库、commit 和本地路径。
- [repo-download-manifest.json](repo-download-manifest.json)：机器可读下载清单。
- [sources.md](sources.md)：网页和源码来源。

源码下载到：

```text
repos/emerging-hot-projects/
```

## 最短结论

这些项目里，“harness”已经明显分成四类：

| 类别 | 代表项目 | 是否使用 harness | 核心价值 |
| --- | --- | --- | --- |
| Agent runtime harness | OpenClaw, Hermes Agent, Dexto | 是 | 把模型包装成可长期运行的 agent：session、tools、memory、permissions、gateway、MCP、skills、observability |
| 软件开发层 harness | openclaw-code-agent, Parallel Code, Bernstein, ccswarm, Claw Orchestrator | 是，很强 | 把 Claude Code、Codex、Gemini、OpenCode 等 coding CLI 放进 plan、worktree、review、merge、PR、audit、retry 的工程闭环 |
| 安全/环境 harness | OpenShell, NemoClaw, HermesClaw | 是 | 用 sandbox、policy、credential routing、network/file/process/inference controls 限制 agent 行为 |
| 生态/能力目录 | awesome-openclaw-skills, awesome-openclaw-agents, agency-agents-zh | 不是核心 harness，但提供 harness 的内容层 | 技能、角色、工作流模板、集成目录，依赖 OpenClaw/Hermes/Claude Code 等宿主 harness 执行 |

## 对你当前研究最重要的发现

1. OpenClaw 和 Hermes Agent 都是完整的 agent runtime harness，但它们的出发点不同。OpenClaw以本地优先的 gateway、多渠道、session/tools/plugins/sandbox 为中心；Hermes 以长期个人 agent、记忆、技能自改进、终端后端和消息网关为中心。
2. “基于 Claude Code / Codex 实现软件开发 harness”的方向已经非常明确。openclaw-code-agent、Parallel Code、Bernstein、ccswarm、Claw Orchestrator 都不是新模型，而是包住现有 coding CLI 的控制层。
3. OpenShell/NemoClaw 代表另一个关键方向：不改 agent 本体，而是在环境层做可执行边界。它们把 filesystem、network、process、inference credentials 变成可声明、可审计、可热更新的 policy。
4. 最值得继续深挖源码的项目，优先级建议是：
   - `openclaw-code-agent`：最贴近“OpenClaw + Claude Code/Codex + worktree/PR”的软件开发 harness。
   - `OpenClaw`：看 gateway/session/tool/plugin/sandbox 这些 runtime 原语。
   - `OpenShell` + `NemoClaw`：看安全边界和 agent 运行环境。
   - `Bernstein`：看确定性调度、审计链、lineage、merge gate 这种偏生产治理的设计。
   - `Dexto`：直接把自己定义为 agent harness，适合作为通用 agent harness 参考。

## 热度信号

GitHub API 查询时间：2026-06-22，时间字段为 UTC。stars/forks 会变化，下列数字只作为本轮研究快照。

| 项目 | Stars | Forks | 最近 pushed_at | 语言 |
| --- | ---: | ---: | --- | --- |
| openclaw/openclaw | 379811 | 79504 | 2026-06-21 | TypeScript |
| NousResearch/hermes-agent | 198917 | 35317 | 2026-06-21 | Python |
| VoltAgent/awesome-openclaw-skills | 50469 | 4907 | 2026-06-16 | N/A |
| NVIDIA/NemoClaw | 21332 | 2844 | 2026-06-21 | TypeScript |
| jnMetaCode/agency-agents-zh | 15344 | 2665 | 2026-06-20 | Shell |
| NVIDIA/OpenShell | 7198 | 872 | 2026-06-19 | Rust |
| mergisi/awesome-openclaw-agents | 3711 | 603 | 2026-05-25 | HTML |
| johannesjo/parallel-code | 740 | 91 | 2026-06-20 | TypeScript |
| truffle-ai/dexto | 634 | 72 | 2026-06-20 | TypeScript |
| sipyourdrink-ltd/bernstein | 578 | 49 | 2026-06-21 | Python |
| Enderfga/claw-orchestrator | 513 | 78 | 2026-06-18 | TypeScript |
| nwiizo/ccswarm | 142 | 14 | 2026-06-10 | Rust/Makefile |
| TheAiSingularity/hermesclaw | 52 | 10 | 2026-04-24 | Shell |
| goldmar/openclaw-code-agent | 40 | 12 | 2026-06-21 | TypeScript |

