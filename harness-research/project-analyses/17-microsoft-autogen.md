# Microsoft AutoGen

Local source: `../../repos/microsoft-autogen`

## What It Is

AutoGen 是 Microsoft 的多 agent AI application framework，目前 README 标注为 maintenance mode，新项目建议转 Microsoft Agent Framework。它仍然是理解多 agent runtime、AgentChat API、tools/workbench、teams 模式的重要参考。

## Harness Architecture

AutoGen 分层：

- Core API：message passing、event-driven agents、local/distributed runtime。
- AgentChat API：面向快速原型的 AssistantAgent、UserProxyAgent、teams/group chat。
- Extensions API：LLM clients、MCP、code execution、外部工具。

核心执行模型不是 coding harness，而是 agent runtime：

`Runtime queue/topic -> Agent message handlers -> model/tool/workbench -> events/messages -> team orchestration`

## Key Source Entry Points

- `python/packages/autogen-core/src/autogen_core/_single_threaded_agent_runtime.py`: single-threaded runtime、message queue、send/publish/response envelopes、intervention handlers、tracing。
- `python/packages/autogen-core/src/autogen_core/tools/`: BaseTool、FunctionTool、Workbench。
- `python/packages/autogen-agentchat/src/autogen_agentchat/agents/_assistant_agent.py`: AssistantAgent tool loop、max_tool_iterations、parallel tool calls、handoffs、memory/model context。
- `python/packages/autogen-agentchat/src/autogen_agentchat/teams/`: round-robin、selector、swarm、graph group chat、Magentic-One。
- `python/packages/autogen-agentchat/src/autogen_agentchat/base/`: agent/team/task contracts。

## Useful Ideas For Our Harness

- 多 agent 系统应有清晰 runtime/message envelope，而不是互相直接调用。
- InterventionHandler 可作为消息级治理/过滤点。
- Workbench 抽象比单工具列表更适合外部 tool servers。
- AgentChat 的 team 模式库适合做多 agent orchestration 参考。

## Caveats

项目进入维护模式，且目标是通用多 agent，不是现代 coding CLI harness。安全、sandbox、repo 操作、长任务恢复要参考 Codex/OpenHands/DeepAgents。

## Suggested Next Reading

读 `AssistantAgent` 的工具迭代说明和 `SingleThreadedAgentRuntime` 的 queue/envelope，实现一个最小 event-driven multi-agent skeleton。
