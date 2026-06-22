# Harness 相关项目清单

本清单按研究价值排序，而不是按流行度排序。

## 第一优先级

| 项目 | 类型 | 为什么值得看 | 建议重点 |
| --- | --- | --- | --- |
| [openai/codex](https://github.com/openai/codex) | Coding agent harness | OpenAI 官方开源 Codex CLI，本地 coding agent 的真实实现 | agent loop、tool execution、sandbox、approval、AGENTS.md、配置 |
| [Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview) | Agent SDK / harness | Anthropic 把 Claude Code 的 tools、agent loop、context management 暴露成 SDK | permissions、hooks、subagents、sessions、context management |
| [anthropics/cwc-long-running-agents](https://github.com/anthropics/cwc-long-running-agents) | Long-running agent harness example | Anthropic 官方示例，展示 goal / evaluator loop、hooks、subagent 等长任务机制 | goal tracking、completion evaluator、checkpoint、resume |
| [Inspect AI](https://inspect.aisi.org.uk/) / [inspect_ai](https://github.com/UKGovernmentBEIS/inspect_ai) | Agent evaluation framework | UK AI Security Institute 的 frontier eval framework，覆盖 coding、agentic tasks、reasoning 等 | Task、Solver、Scorer、tools、sandbox、logs |
| [SWE-bench](https://github.com/swe-bench/SWE-bench) | Coding-agent benchmark | 真实 GitHub issue + repo + tests，是 coding agent 研究核心 benchmark | task instance、repo setup、patch validation、Docker 环境 |

## 第二优先级

| 项目 | 类型 | 为什么值得看 | 建议重点 |
| --- | --- | --- | --- |
| [OpenAI Evals](https://github.com/openai/evals) | LLM/system eval framework | OpenAI 官方 eval framework，适合研究自定义 eval 和私有工作流评测 | eval registry、custom eval、dataset、grader |
| [OpenAI Agents SDK](https://developers.openai.com/api/docs/guides/agents) | Agent application framework | OpenAI 面向应用开发的 agent runtime，强调 tools、handoffs、approvals、state | tracing、guardrails、human review、workflow evals |
| [EleutherAI lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness) | Model evaluation harness | 经典 LLM benchmark runner，任务覆盖广，adapter 成熟 | task YAML、model adapter、batch eval、结果汇总 |
| [Terminal-Bench](https://github.com/harbor-framework/terminal-bench) | Terminal agent benchmark | 测试 agent 在真实终端环境里完成端到端任务 | terminal task protocol、containerized eval、failure taxonomy |
| [Harbor](https://github.com/harbor-framework/harbor) | Agent eval / RL framework | Terminal-Bench 团队的新框架，用于运行 agent benchmarks 和相关实验 | agent adapter、environment、benchmark orchestration |

## Agent 框架和平台

| 项目 | 类型 | 为什么值得看 | 建议重点 |
| --- | --- | --- | --- |
| [LangChain Deep Agents](https://docs.langchain.com/oss/python/deepagents/overview) | Agent harness framework | 明确自称 batteries-included agent harness，内置 planning、filesystem、subagents、human-in-the-loop | context engineering、virtual filesystem、subagents |
| [HKUDS/OpenHarness](https://github.com/HKUDS/OpenHarness) | Community agent harness | 社区项目，直接围绕 agent loop、tools、skills、memory、permissions、hooks、多 agent 协调组织 | 架构分层、plugin/tool 接口、memory |
| [OpenHands](https://github.com/OpenHands/openhands) | Coding agent platform | 开源、model-agnostic coding agent platform，强调真实工程任务、sandbox 和评测 | sandbox、browser/terminal/code integration、agent controller |
| [OpenHands Software Agent SDK](https://github.com/OpenHands/software-agent-sdk) | Coding agent SDK | 用 Python / REST API 构建能处理代码任务的 agents，可用本地或临时 workspace | workspace abstraction、agent server、one-off / routine tasks |
| [Google ADK](https://adk.dev/) / [google/adk-python](https://github.com/google/adk-python) | Agent development framework | Google 的 open-source code-first agent framework，覆盖 build、debug、deploy、eval | tools、multi-agent、deployment、evaluation |
| [Microsoft Agent Framework](https://learn.microsoft.com/en-us/agent-framework/overview/) | Enterprise agent framework | Microsoft 把 AutoGen 抽象和 Semantic Kernel 企业能力合并后的方向 | state、middleware、telemetry、graph workflows |
| [Microsoft AutoGen](https://github.com/microsoft/autogen) | Multi-agent framework | 经典多 agent 框架；截至本轮研究，GitHub README 提示进入 maintenance mode | 历史设计、multi-agent patterns；新项目优先看 Agent Framework |

## Eval 工具补充

| 项目 | 类型 | 为什么值得看 | 建议重点 |
| --- | --- | --- | --- |
| [DeepEval](https://github.com/confident-ai/deepeval) | LLM app eval framework | 类似 pytest 的 LLM evaluation framework，覆盖 task completion、hallucination、answer relevancy 等 | unit-test style eval、LLM-as-judge、CI 集成 |
| [promptfoo](https://www.promptfoo.dev/) | Prompt / LLM app testing | 常用于 prompt regression、provider 对比、CI eval，也支持 Claude Agent SDK provider | test matrix、assertions、provider adapter |
| [AGENTS.md](https://agents.md/) | Agent instruction format | 跨 coding agents 的项目指令约定 | 项目级指令格式、setup/test/style/checklist |

## 阅读顺序建议

1. 先读 OpenAI Codex agent loop 和 Anthropic trustworthy agents，建立术语边界。
2. 再读 `openai/codex` 和 Claude Agent SDK，理解真实 agent harness 如何组织。
3. 然后读 Inspect AI 和 SWE-bench，理解如何评测 agent 行为。
4. 最后看 Deep Agents、OpenHarness、OpenHands、Google ADK、Microsoft Agent Framework，比较不同框架的抽象取舍。

## 源码拆解问题清单

读每个项目时可以问：

- 它的 agent loop 在哪里？
- 它如何构造上下文？
- 它如何定义工具 schema？
- 它如何处理工具失败？
- 它如何做权限审批？
- 它有没有 sandbox？
- 它如何记录 trace？
- 它如何恢复长任务？
- 它如何把失败变成 eval？
- 它的扩展点是 plugin、hook、middleware、tool，还是 adapter？
