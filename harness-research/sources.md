# Sources

本文件记录第一轮研究引用的资料入口。优先记录官方文档、官方博客、官方 GitHub 仓库和主项目文档。

访问日期：2026-06-20

## OpenAI

- [Unrolling the Codex agent loop](https://openai.com/index/unrolling-the-codex-agent-loop/)：Codex harness / agent loop 的核心说明。重点看 user、model、tools 之间的编排。
- [Harness engineering: leveraging Codex in an agent-first world](https://openai.com/index/harness-engineering/)：OpenAI 对 harness engineering 的实践总结。重点看环境设计、意图表达和反馈循环。
- [Introducing workspace agents in ChatGPT](https://openai.com/index/introducing-workspace-agents-in-chatgpt/)：ChatGPT Workspace Agents 的产品说明。重点看 shared agents、cloud workspace、permissions、controls。
- [OpenAI Codex GitHub repository](https://github.com/openai/codex)：Codex CLI 开源实现。重点看 agent loop、tools、sandbox、approval 和配置。
- [OpenAI Agents SDK guide](https://developers.openai.com/api/docs/guides/agents)：Agents SDK 的官方入口。重点看 orchestration、tool execution、approvals 和 state。
- [Guardrails and human review](https://developers.openai.com/api/docs/guides/agents/guardrails-approvals)：OpenAI 对 guardrails / human review 的说明。
- [OpenAI Agents SDK tracing](https://openai.github.io/openai-agents-python/tracing/)：tracing 记录 LLM generations、tool calls、handoffs、guardrails 和 custom events。
- [Evaluate agent workflows](https://developers.openai.com/api/docs/guides/agent-evals)：OpenAI 平台用 traces、graders、datasets、eval runs 评测 agent workflow。
- [Custom instructions with AGENTS.md](https://developers.openai.com/codex/guides/agents-md)：Codex 如何读取和分层项目指令。
- [Codex sandboxing](https://developers.openai.com/codex/concepts/sandboxing)：Codex sandbox 的信任边界和 approval fatigue 问题。
- [Codex agent approvals and security](https://developers.openai.com/codex/agent-approvals-security)：Codex sandboxing、approvals、network access 的安全说明。
- [OpenAI Evals](https://github.com/openai/evals)：OpenAI 官方 eval framework。

## Anthropic

- [Trustworthy agents in practice](https://www.anthropic.com/research/trustworthy-agents)：Anthropic 对 model、harness、tools、environment 四层结构的说明。
- [Building effective agents](https://www.anthropic.com/research/building-effective-agents)：Anthropic 对 reliable agents 的设计模式总结。
- [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)：长任务 agent harness 的实践文章。
- [Harness design for long-running application development](https://www.anthropic.com/engineering/harness-design-long-running-apps)：长时间自主软件工程中的 harness design。
- [Claude Agent SDK overview](https://code.claude.com/docs/en/agent-sdk/overview)：Claude Agent SDK 官方入口，说明 tools、agent loop、context management。
- [Claude Code hooks reference](https://code.claude.com/docs/en/hooks)：Claude Code hooks 参考。
- [Claude Agent SDK hooks](https://code.claude.com/docs/en/agent-sdk/hooks)：SDK 中用 hooks 拦截和控制 agent 行为。
- [Claude Code permissions](https://code.claude.com/docs/en/permissions)：Claude Code allow / ask / deny 权限规则。
- [Claude Code permission modes](https://code.claude.com/docs/en/permission-modes)：Claude Code 编辑和命令执行的权限模式。
- [Claude Managed Agents overview](https://platform.claude.com/docs/en/managed-agents/overview)：Anthropic 对 managed agent harness 的产品化说明。
- [anthropics/claude-agent-sdk-python](https://github.com/anthropics/claude-agent-sdk-python)：Claude Agent SDK Python 仓库。
- [anthropics/claude-agent-sdk-typescript](https://github.com/anthropics/claude-agent-sdk-typescript)：Claude Agent SDK TypeScript 仓库。
- [anthropics/claude-agent-sdk-demos](https://github.com/anthropics/claude-agent-sdk-demos)：Claude Agent SDK demos。
- [anthropics/cwc-long-running-agents](https://github.com/anthropics/cwc-long-running-agents)：长任务 agent harness 示例。

## Evaluation Harness / Benchmarks

- [EleutherAI lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness)：统一评测 generative language models 的经典框架。
- [lm-evaluation-harness task guide](https://github.com/EleutherAI/lm-evaluation-harness/blob/main/docs/task_guide.md)：任务配置方式。
- [Inspect AI docs](https://inspect.aisi.org.uk/)：UK AI Security Institute 的 LLM / agent eval framework 文档。
- [inspect_ai GitHub repository](https://github.com/UKGovernmentBEIS/inspect_ai)：Inspect AI 源码。
- [inspect_evals GitHub repository](https://github.com/UKGovernmentBEIS/inspect_evals)：Inspect evals 集合。
- [SWE-bench GitHub repository](https://github.com/swe-bench/SWE-bench)：真实 GitHub issue 的 coding-agent benchmark。
- [SWE-bench paper](https://arxiv.org/abs/2310.06770)：SWE-bench 论文入口。
- [Terminal-Bench](https://github.com/harbor-framework/terminal-bench)：真实终端环境中的 agent benchmark。
- [Terminal-Bench website](https://www.tbench.ai/)：Terminal-Bench 项目入口。
- [Harbor](https://github.com/harbor-framework/harbor)：Terminal-Bench 团队的新 benchmark / agent eval framework。
- [Terminal-Bench 2.0 paper](https://arxiv.org/html/2601.11868v1)：Terminal-Bench 2.0 论文入口。

## Other Agent Frameworks / Platforms

- [LangChain Deep Agents overview](https://docs.langchain.com/oss/python/deepagents/overview)：LangChain 的 agent harness 框架说明。
- [deepagents GitHub repository](https://github.com/langchain-ai/deepagents)：Deep Agents 源码。
- [HKUDS/OpenHarness](https://github.com/HKUDS/OpenHarness)：社区 agent harness 项目。
- [OpenHands](https://github.com/OpenHands/openhands)：开源 coding agent platform。
- [OpenHands Software Agent SDK](https://github.com/OpenHands/software-agent-sdk)：构建 coding agents 的 Python / REST SDK。
- [OpenHands website](https://openhands.dev/)：OpenHands 产品和平台说明。
- [Google ADK](https://adk.dev/)：Google Agent Development Kit 官方站点。
- [google/adk-python](https://github.com/google/adk-python)：Google ADK Python 仓库。
- [Microsoft Agent Framework overview](https://learn.microsoft.com/en-us/agent-framework/overview/)：Microsoft Agent Framework 官方文档。
- [Microsoft AutoGen](https://github.com/microsoft/autogen)：经典多 agent 框架；本轮研究时 README 显示进入 maintenance mode。
- [DeepEval](https://github.com/confident-ai/deepeval)：开源 LLM evaluation framework。
- [promptfoo](https://www.promptfoo.dev/)：prompt / LLM app 测试与回归工具。
- [AGENTS.md](https://agents.md/)：跨 agent 的项目指令约定。
