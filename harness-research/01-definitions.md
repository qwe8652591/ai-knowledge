# Harness 的定义和边界

`harness` 在 LLM / agent 领域没有唯一标准定义。根据 OpenAI、Anthropic、LangChain、eval 社区和 coding-agent 项目的用法，可以先把它拆成三类：agent harness、evaluation harness、workflow/product harness。

## 工作定义

Agent harness：模型外部的执行壳层。它把用户目标、模型推理、工具调用、观察结果、状态、权限、日志和反馈循环连接起来，让模型能持续行动。

Evaluation harness：标准化评测壳层。它把 dataset、任务协议、模型/agent adapter、环境、scorer 和输出日志统一起来，让不同模型或不同 harness 的效果可复现、可比较。

Workflow/product harness：面向业务工作流的 agent 执行平台。它通常把 shared agents、connected apps、scheduled triggers、审批、组织权限、监控和治理整合起来。

## OpenAI / Codex

OpenAI 的 Codex agent loop 文章把 Codex harness 描述为一层核心 agent loop 和执行逻辑，负责协调用户、模型和工具，让模型可以完成真实软件工程任务。

OpenAI 的 harness engineering 文章强调：当团队主要依靠 Codex agents 工作时，工程师的工作会从“直接写代码”转向“设计环境、指定意图、构建反馈循环”。这说明 OpenAI 语境里的 harness 不只是一段系统提示词，而是一整套让 agent 能可靠工作的工程环境。

对 Codex 来说，harness 至少包含：

- agent loop：用户输入、模型推理、工具调用、观察结果、继续推理。
- context construction：把指令、项目文件、历史、工具结果放进模型上下文。
- tool execution：文件读取、编辑、命令运行、浏览器、MCP 等工具。
- approvals and sandbox：低风险动作自动执行，高风险动作需要边界或审批。
- project guidance：例如 `AGENTS.md`、skills、repo docs、测试命令。
- feedback loop：测试、lint、review、CI、trace、eval。

## ChatGPT Workspace Agents

ChatGPT Workspace Agents 是 OpenAI 对业务工作流 agent 的产品化表达。它们是 Codex-powered cloud agents，可以跨文件、代码、工具、memory 和 connected apps 完成长期或重复工作，同时受组织权限、审批和监控约束。

这里的 harness 更像“组织级工作流运行时”：

- agent builder：把重复流程转成共享 agent。
- cloud workspace：让 agent 能持续执行，不依赖用户本地会话。
- tool connectors：Slack、Linear、文档、代码、业务系统等。
- governance：组织权限、审批、可见性、监控。
- reuse：团队共享、模板化、持续改进。

## OpenAI Agents SDK

OpenAI Agents SDK 把 agent 定义为能计划、调用工具、协作并保留状态完成多步工作的应用。官方文档强调：当应用需要自己拥有 orchestration、tool execution、approvals 和 state 时，使用 Agents SDK。

这说明在 SDK 场景里，harness 重点是 application-owned runtime：

- 由应用定义 agents、tools、handoffs、state。
- 用 guardrails 和 human review 决定 run 是继续、暂停还是停止。
- 用 tracing 记录 LLM generations、tool calls、handoffs、guardrails 和 custom events。
- 用 evals、datasets 和 graders 检查 agent workflow 质量。

## Anthropic

Anthropic 在 trustworthy agents 文章中给出了非常清晰的四层模型：

- model：核心能力来源。
- harness：模型运行时遵守的 instructions 和 guardrails。
- tools：agent 能调用的外部能力。
- environment：工具和 agent 所处的外部环境。

这个定义的价值在于它把风险责任拆开：即使 model 本身训练得很好，配置糟糕的 harness、过度开放的 tools 或暴露的 environment 仍然可能让 agent 被利用。

Claude Agent SDK 则提供了更工程化的 harness 形态：它把 Claude Code 的 tools、agent loop、context management、permissions、hooks 和 subagents 作为 Python / TypeScript SDK 暴露出来。Anthropic 还特别讨论了 long-running agents：长任务需要 context compaction、结构化计划、持续检查和可恢复机制。

## LangChain Deep Agents

LangChain Deep Agents 把自己定位为用于构建更复杂 agent 的 batteries-included agent harness。它强调：

- planning tool
- virtual filesystem
- subagents
- human-in-the-loop
- context engineering

它的定义接近“可嵌入应用里的 agent runtime”，不是只做 benchmark 的 eval harness。

## Evaluation 社区

EleutherAI 的 lm-evaluation-harness 是典型 evaluation harness：用统一框架在大量任务上测试 generative language models。

OpenAI Evals、Inspect AI、SWE-bench、Terminal-Bench / Harbor 也属于 evaluation harness 或 agent evaluation framework。它们的共同点不是“让 agent 工作”，而是“让工作结果可复现地被度量”。

## 边界判断

一个系统可以被称为 harness，通常需要满足至少三个条件：

1. 它在模型外部，负责连接模型与任务世界。
2. 它定义了模型能看到什么、能做什么、何时停止、如何验证。
3. 它让行为可重复、可观察、可约束或可评测。

只有 prompt，不算完整 harness。只有 API wrapper，也不算完整 harness。只有 benchmark dataset，也不算完整 evaluation harness，除非它还包含 runner、adapter、scorer 和复现机制。
