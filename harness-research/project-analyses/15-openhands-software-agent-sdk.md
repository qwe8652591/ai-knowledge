# OpenHands Software Agent SDK

Local source: `../../repos/software-agent-sdk`

## What It Is

OpenHands Software Agent SDK 是面向 coding agents 的 Python/REST SDK。它提供 Agent、Conversation、Workspace、Tool、Event、Agent Server 等分层，可在本地 workspace 或远程/容器/云 workspace 中运行。

## Harness Architecture

核心层次：

`Conversation(factory) -> LocalConversation/RemoteConversation -> Agent.step loop -> LLM -> tool calls -> ParallelToolExecutor -> Workspace/tools -> events/state/hooks/condenser -> next iteration`

Server 层再把 conversation、workspace、events、tools、settings、MCP、Git、terminal 暴露为 REST/WebSocket API。

## Key Source Entry Points

- `openhands-sdk/openhands/sdk/conversation/conversation.py`: Conversation factory，根据 Local/RemoteWorkspace 返回本地或远程 conversation。
- `openhands-sdk/openhands/sdk/conversation/impl/local_conversation.py`: 本地 run/arun、plugin lazy loading、hooks、state/event store、stuck detection、condenser、client tools。
- `openhands-sdk/openhands/sdk/agent/agent.py`: Agent 主循环、LLM response 分类、tool call validation、parallel execution、FinishTool 截断、context window condensation。
- `openhands-sdk/openhands/sdk/tool/tool.py`: ToolDefinition、ToolExecutor、annotations、DeclaredResources、MCP export。
- `openhands-sdk/openhands/sdk/workspace/base.py`: workspace interface，execute_command、file upload/download、git changes/diff、pause/resume。
- `openhands-tools/openhands/tools/terminal/`: terminal tool/session 实现。
- `openhands-agent-server/openhands/agent_server/conversation_service.py`: server conversation、worktree、profiles、event service。

## Useful Ideas For Our Harness

- Conversation 是很好的顶层 API：隐藏本地/远程 runtime 差异。
- Workspace 抽象把 agent 行动边界统一成 command/file/git 操作。
- ToolDefinition 同时表达 action schema、observation schema、executor、annotations、resource locks，有利于安全并发。
- resource declaration 用于并行工具执行时加锁，避免文件/终端状态竞态。
- event sourcing、callbacks、token callbacks、visualizer、stuck detector 是生产 SDK 的关键配套。
- Agent Server 把 runtime 变成可被 UI/automation 调用的服务，和 Agent Canvas 分工清楚。

## Caveats

代码量大，模块多。建议不要从 server API 开始读；先读 SDK local conversation 与 Agent，再看 tools/workspace，最后看 server。

## Suggested Next Reading

读 `agent/agent.py` 的 `step()`/`astep()`，然后读 `tool/tool.py` 和 terminal/file_editor tools，最后看 `conversation_service.py` 的 worktree 策略。
