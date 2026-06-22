# OpenHarness

Local source: `../../repos/openharness`

## What It Is

OpenHarness 是一个开源 Python agent harness，README 明确把 harness 定义为围绕 LLM 的工具、知识、观察、行动、权限、记忆、多 agent 基础设施。它还包含 ohmo 个人 agent、TUI、MCP、channels、swarm、memory 等产品化组件。

## Harness Architecture

核心 loop 在 `engine/query.py`：

`ConversationMessage/history -> streaming API client -> text/tool deltas -> ToolRegistry -> PermissionChecker -> HookExecutor -> ToolResult -> next model turn -> compact/retry/status events`

它是比 Codex 更容易读的 Python 版本 tool-aware loop，适合快速理解 harness 主路径。

## Key Source Entry Points

- `src/openharness/engine/query.py`: 核心 tool-aware query loop、auto compact、permission prompt、hooks、metadata tracking。
- `src/openharness/tools/base.py`: `BaseTool`、`ToolResult`、`ToolRegistry`。
- `src/openharness/permissions/checker.py`: permission modes、allowed/denied tools、path rules、sensitive path deny、command deny。
- `src/openharness/api/`: OpenAI/Codex/Copilot 等 provider client。
- `src/openharness/memory/`: memory manager/search/schema。
- `src/openharness/swarm/`、`tests/test_swarm/`: multi-agent/team lifecycle。
- `ohmo/`: 长会话个人 agent 与 chat gateway。

## Useful Ideas For Our Harness

- `ToolExecutionContext` 持有 cwd、metadata、hook_executor，简单但实用。
- permission checker 应内置不可覆盖的 sensitive path deny list。
- plan/default/full-auto modes 这样的权限模式有助于产品体验。
- query loop 里主动跟踪 read files、skills、async agent events、verified work，有助于 compact 后保留任务焦点。
- dry-run/preview 能提高 harness 配置可调试性。

## Caveats

项目相对新，工程成熟度和安全边界不能直接等同 Codex/Claude Code。适合作学习和快速实验参考。

## Suggested Next Reading

读 `engine/query.py` 的 tool call 执行部分和 `permissions/checker.py`；对照 Codex 的 Rust handler 看同一问题两种实现风格。
