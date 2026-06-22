# DeepAgents

Local source: `../../repos/deepagents`

## What It Is

DeepAgents 是 LangChain/LangGraph 生态里的 opinionated agent harness。它把 filesystem、shell、subagents、memory、skills、summarization、HITL permissions 做成默认可用的 harness，而不是让用户从裸 LangGraph 组装。

仓库是 monorepo：`libs/deepagents` 是 SDK/harness，`libs/code` 是 terminal coding agent 产品壳。

## Harness Architecture

SDK 主线：

`create_deep_agent(model/tools/system_prompt/middleware/subagents/skills/memory/permissions/backend) -> LangChain create_agent + middleware stack -> LangGraph CompiledStateGraph`

它的设计中心是 middleware/backends：

- middleware 注入 filesystem tools、subagents/task tool、skills、memory、summarization、permissions。
- backends 抽象 state/filesystem/local shell/sandbox/langsmith/context hub/composite。

## Key Source Entry Points

- `libs/deepagents/deepagents/graph.py`: `create_deep_agent()`，组装默认模型、prompt、middleware、required middleware、HITL、profile。
- `libs/deepagents/deepagents/middleware/filesystem.py`: 文件工具与 path-level permission。
- `libs/deepagents/deepagents/middleware/subagents.py`: `task` tool、SubAgent/CompiledSubAgent contract。
- `libs/deepagents/deepagents/backends/protocol.py`: filesystem/sandbox backend protocol。
- `libs/deepagents/deepagents/backends/sandbox.py`: 基于 sandbox execute/upload 派生 read/write/edit/glob/grep。
- `libs/code/deepagents_code/agent.py`: terminal coding agent 产品层，shell allow-list、CLI agent 创建、skills/memory/local context。
- `libs/code/deepagents_code/subagents.py`: 从 `.deepagents/agents/{name}/AGENTS.md` 加载 subagent。

## Useful Ideas For Our Harness

- 功能最好通过 middleware/backends 可组合，而不是堆在一个 Agent 类里。
- required middleware 需要保护，避免 profile/exclusion 把核心能力移除。
- subagent 描述应包含 name/description/system_prompt/model/tools/permissions/skills/response_format。
- backend protocol 让同一工具语义可以跑在 local、state、remote sandbox、cloud sandbox 上。
- security 明确写成“trust the LLM; enforce boundaries at tool/sandbox level”是健康心智模型。

## Caveats

它依赖 LangChain/LangGraph，适合 Python 生态。若我们要做轻量 harness，可借鉴 middleware/backends 思路，但不一定引入整套依赖。

## Suggested Next Reading

深读 `graph.py` 中 `create_deep_agent()` 后半段，以及 filesystem middleware 里工具注册和权限如何接入 HITL。
