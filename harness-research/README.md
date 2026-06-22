# Harness 研究笔记

本目录记录对 AI agent / LLM evaluation harness 的第一轮研究。当前结论：`harness` 不是单纯的 prompt，而是包在模型外面的执行系统，负责上下文、工具、权限、状态、评测、日志和反馈循环。

研究日期：2026-06-20

## 目录

- [01-definitions.md](01-definitions.md)：OpenAI、ChatGPT Workspace Agents、Anthropic 等对 harness 的定义和边界。
- [02-agent-harness-practices.md](02-agent-harness-practices.md)：构建 agent harness 的实践模式、组件和反模式。
- [03-evaluation-harness.md](03-evaluation-harness.md)：evaluation harness 的组成、评测维度和实验建议。
- [04-project-catalog.md](04-project-catalog.md)：值得研究的项目清单，包括 OpenAI Codex、Claude Agent SDK、Inspect、SWE-bench 等。
- [source-repos.md](source-repos.md)：已经下载到本地的源码项目索引和第一轮阅读路线。
- [project-analyses/README.md](project-analyses/README.md)：18 个已下载项目的逐项源码分析、对比矩阵和推荐阅读顺序。
- [project-analyses/19-cross-project-comparison.md](project-analyses/19-cross-project-comparison.md)：横向比较这些项目的优劣、适用场景和设计原因。
- [software-development-harness/README.md](software-development-harness/README.md)：另一个维度，研究基于 Claude Code、Codex 等 coding agents 的软件开发层 harness，包括 PR/CI、worktree、任务系统、方法论和技能/插件框架。
- [sources.md](sources.md)：本轮研究引用的官方文档、项目和文章入口。

源码目录位于 `../repos/`。

## 快速结论

### 1. Harness 有两种主语境

Agent harness：把 LLM 变成可执行任务的 agent。它通常包含 agent loop、工具调用、状态管理、上下文管理、权限审批、沙箱、hook、日志、trace、恢复和人工介入。

Evaluation harness：把模型或 agent 放到统一任务协议里评测。它通常包含 dataset、task runner、model/agent adapter、environment/sandbox、scorer、metrics、logs 和 reproducibility 配置。

### 2. OpenAI / Codex 的重点

OpenAI 的 Codex 资料把 harness 描述成支撑 Codex 体验的核心 agent loop 和执行逻辑。OpenAI 的 harness engineering 文章强调，团队的工作从直接写代码转向设计环境、明确意图、构建反馈循环，让 Codex agents 能可靠工作。

### 3. Anthropic 的重点

Anthropic 在 trustworthy agents 文章里把 agent 拆成四层：model、harness、tools、environment。其中 harness 主要是模型运行时遵守的 instructions 和 guardrails。Anthropic 的 Claude Agent SDK 则把 Claude Code 的工具、agent loop、context management、permissions、hooks 和 subagents 作为可编程能力暴露出来。

### 4. 实践上最重要的不是“提示词写得更长”

更可靠的方向是：

- 把项目知识结构化沉淀到版本控制里。
- 给 agent 明确的工具边界和权限模式。
- 用 sandbox 和 approvals 降低风险。
- 用 traces 记录完整执行路径。
- 把失败 trace 转成可回归的 eval。
- 让 hook / guardrail 做 deterministic enforcement。
- 让长任务有计划、状态、检查点和恢复机制。

## 后续研究方向

1. 源码拆解：优先读 `openai/codex`、`claude-agent-sdk-python`、`anthropics/cwc-long-running-agents`。
2. Eval 复现：用 `Inspect AI` 或 `SWE-bench` 跑一个最小 coding-agent benchmark。
3. 自建 harness 原型：先做一个小型 agent loop，包含 tool registry、permission gate、trace log、eval runner。
4. 对比实验：同一任务分别用 prompt-only、tool-only、agent harness、agent harness + eval feedback 跑，观察差异。
