# Claude Agent SDK Python

Local source: `../../repos/claude-agent-sdk-python`

## What It Is

Claude Agent SDK Python 是 Claude Code 能力的 Python 控制 SDK。它本身不是从零实现 agent loop，而是启动/控制 Claude Code CLI，通过 stream-json/control protocol 暴露 query、interactive client、permission callbacks、hooks、MCP servers、session store、transcript mirror 等能力。

## Harness Architecture

核心模式是“SDK 作为控制面，CLI 作为 runtime”：

`Python app -> ClaudeSDKClient/query -> subprocess Claude CLI -> stream-json messages/control requests -> callbacks/hooks/session store -> app`

SDK 做的关键事情是进程管理、消息解析、双向控制协议、Python in-process MCP server 注入、权限回调、hook callback、session resume 和 transcript mirroring。

## Key Source Entry Points

- `src/claude_agent_sdk/query.py`: 一次性 async `query()`，文档中明确 interactive state 应使用 `ClaudeSDKClient`。
- `src/claude_agent_sdk/client.py`: 双向 client 入口。
- `src/claude_agent_sdk/_internal/query.py`: control request/response、hook callbacks、permission callbacks、message streaming、SDK MCP servers、skills、session mirror。
- `src/claude_agent_sdk/_internal/transport/subprocess_cli.py`: CLI 查找、命令行参数构造、`--output-format stream-json`、permission mode、allowed/disallowed tools、resume/session-id、MCP config、plugins、settings 等。
- `src/claude_agent_sdk/types.py`: `ClaudeAgentOptions`、permission/hook/session 类型。
- `examples/tool_permission_callback.py`: 最直观展示 `can_use_tool` 如何 allow/deny/modify input。

## Useful Ideas For Our Harness

- 如果已有成熟 CLI harness，可以先做 SDK/control plane，而不是重写 runtime。
- `allowed_tools` 和 `disallowed_tools` 的语义要明确：allowlist 可能只是 auto-approve，不等同于隐藏工具。
- permission callback 应能返回 allow、deny、updated input，这比简单布尔审批更实用。
- session store、transcript mirror、resume 是产品嵌入场景的刚需。

## Caveats

Runtime 在 Claude CLI 里，Python SDK 能解释控制协议但无法完全展示底层 tool loop、安全策略实现。源码分析时要把“SDK 能力”和“CLI runtime 能力”分清。

## Suggested Next Reading

继续读 `_internal/transport/subprocess_cli.py` 的参数构造与 `_internal/query.py` 的 control protocol；结合 demos 看 UI/产品如何消费消息流。
