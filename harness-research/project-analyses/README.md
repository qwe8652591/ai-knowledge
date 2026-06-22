# Harness Project Analyses

本目录是对 `repos/` 下已下载项目的逐项源码分析。重点不是复述 README，而是看每个项目把 harness 的核心问题放在哪里：任务入口、agent loop、工具协议、上下文/记忆、沙箱/权限、评测与日志。

## Quick Matrix

| 项目 | 类型 | 最值得看的点 | 适合我们借鉴的方向 | 深挖优先级 |
| --- | --- | --- | --- | --- |
| OpenAI Codex | 本地 coding agent harness | Rust session/turn、tool handler、sandbox、approval、hooks、多 agent | 生产级 coding harness 的完整边界设计 | P0 |
| Claude Agent SDK Python | Claude Code 控制 SDK | subprocess CLI transport、stream-json、tool permission callback、hooks、session store | 把成熟 CLI harness 嵌入应用的 SDK 模式 | P0 |
| Claude Agent SDK TypeScript | SDK 包装与 session-store 示例 | SessionStore contract、changelog 暴露的 runtime 能力 | TS 侧持久化/恢复适配器设计 | P2 |
| Claude Agent SDK Demos | 产品化嵌入示例 | WebSocket streaming、session resume、UI tool/result 渲染 | 从 SDK 到产品体验的胶水层 | P1 |
| CWC Long Running Agents | 长任务 primitives | default-fail、fresh-context evaluator、handoff notes、hooks | 长跑任务防自欺与可恢复机制 | P0 |
| Inspect AI | 评测 harness 框架 | Task/Solver/Scorer/log/sandbox 抽象 | 设计我们自己的 eval harness | P0 |
| Inspect Evals | Inspect AI eval 集合 | 社区 eval packaging、register 机制、大量任务示例 | 评测资产组织与贡献流程 | P2 |
| SWE-bench | 代码修复 benchmark harness | Docker image/test spec、patch apply、fail-to-pass/pass-to-pass 判定 | coding benchmark 的可复现执行与评分 | P0 |
| lm-evaluation-harness | 传统 LM eval harness | model adapter、task registry、request cache、metrics | 非 agent 模型评测的成熟任务/指标模式 | P1 |
| Terminal-Bench | terminal agent benchmark | Docker/tmux terminal、agent adapters、test parser、run resume | 真实终端任务评测 | P1 |
| Harbor | 新一代 agent benchmark runner | Job/Trial/Environment/Agent/Verifier、cloud env、network policy | 通用 agent benchmark 执行平台 | P0 |
| DeepAgents | LangChain/LangGraph agent harness | middleware/backends、filesystem、subagents、skills、permissions | 可组合 agent harness 架构 | P0 |
| OpenHarness | 开源教学/实践 harness | Python tool-aware loop、permissions、memory、MCP、swarm、TUI | 快速理解 harness 的可读实现 | P1 |
| OpenHands Agent Canvas | 控制台/产品壳 | 多 backend agent control center、automation、ACP | 产品层 orchestrator/UI | P2 |
| OpenHands Software Agent SDK | 生产 coding agent SDK/runtime | Conversation/Agent/Workspace/Tool/Event/Server | Python coding agent SDK 架构 | P0 |
| Google ADK Python | 通用 agent/workflow 框架 | Runner、Agent tree、Workflow runtime、sessions、plugins、artifacts | 企业应用 agent 框架 | P1 |
| Microsoft AutoGen | 多 agent 框架 | Core runtime、AgentChat、tools/workbench、teams | 多 agent 消息运行时与模式库 | P2 |
| OpenAI Evals | OpenAI eval framework | Eval/Solver/registry/recorder/model-graded evals | 传统 eval registry 与 solver 设计 | P1 |

## Cross-Project Takeaways

1. Agent harness 和 evaluation harness 是两类东西。前者解决“模型如何安全、持续地行动”，后者解决“如何可复现地判断结果”。Codex、Claude SDK、DeepAgents、OpenHands SDK 属于前者；Inspect AI、SWE-bench、Harbor 属于后者或混合体。
2. 现代 agent harness 的共同骨架是：`context assembly -> model call -> tool calls -> permission/sandbox/hooks -> observations -> state/log -> next turn`。
3. 长任务能力不只是更大的上下文。CWC、Codex、DeepAgents、OpenHands SDK 都强调 compact/resume/handoff/subagent/verification。
4. benchmark harness 的关键不是调用模型，而是环境复现、日志结构、评分器、失败归因、可恢复运行。Harbor 和 SWE-bench 最清楚。
5. 可扩展性的两条路线不同：DeepAgents 用 middleware/backend 组合，OpenHands SDK 用 Conversation/Workspace/Tool/Event 分层，Codex 用 Rust crate 和 protocol/tool handler 边界。

## Recommended Reading Order

1. 先读 [OpenAI Codex](01-openai-codex.md)、[Claude Agent SDK Python](02-claude-agent-sdk-python.md)、[DeepAgents](12-deepagents.md)、[OpenHands Software Agent SDK](15-openhands-software-agent-sdk.md)，建立 agent harness 设计图。
2. 再读 [Inspect AI](06-inspect-ai.md)、[SWE-bench](08-swe-bench.md)、[Harbor](11-harbor.md)，建立 evaluation harness 设计图。
3. 最后读 [CWC Long Running Agents](05-cwc-long-running-agents.md)、[Terminal-Bench](10-terminal-bench.md)、[OpenHarness](13-openharness.md)，提取具体实践模式。

## Cross-Project Comparison

- [19-cross-project-comparison.md](19-cross-project-comparison.md)：横向比较这些项目的优劣、适用场景，以及它们为什么这样设计。
