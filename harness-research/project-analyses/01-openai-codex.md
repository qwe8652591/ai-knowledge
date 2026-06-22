# OpenAI Codex

Local source: `../../repos/openai-codex`

## What It Is

OpenAI Codex 是一个完整的本地 coding agent harness/runtime，而不是单纯 SDK。它覆盖 CLI/TUI、session/turn、上下文装配、工具执行、sandbox、权限审批、hooks、MCP、skills、多 agent、线程/历史存储等端到端能力。

## Harness Architecture

核心链路可以概括为：

`CLI/TUI -> session/turn -> AGENTS.md/context assembly -> model -> tool handlers -> sandbox/approval/hooks -> event stream/log -> next turn`

它把 agent loop 的硬边界拆得很细：模型协议、工具协议、执行策略、sandbox policy、approval policy、thread state、hook events 都是独立模块。这是生产级 harness 的典型形态，复杂但边界清楚。

## Key Source Entry Points

- `codex-rs/core/src/session/`: session、turn、turn context、多 agent 与预算相关逻辑。
- `codex-rs/core/src/agents_md.rs`: 从项目根到 cwd 发现并拼接 `AGENTS.md`/override 指令，是上下文治理入口。
- `codex-rs/core/src/tools/handlers/unified_exec/exec_command.rs`: `exec_command` tool handler，集中体现 cwd、sandbox、approval、hooks、process manager、remote shell 等执行策略。
- `codex-rs/core/src/agent/control.rs`: subagent/multi-agent 控制面。
- `codex-rs/core/src/codex_thread.rs`: thread 级 approval/sandbox/permission 状态。
- `codex-rs/hooks/`、`codex-rs/sandboxing/`、`codex-rs/execpolicy/`: harness 安全与可观测边界。

## Useful Ideas For Our Harness

- 把“工具是否能跑”和“工具怎么跑”分开：tool handler 负责语义，sandbox/permission/approval 决定执行边界。
- project instructions 需要明确 discovery 规则、大小限制、覆盖顺序和 host/user/project 优先级。
- hooks 是 harness 的一等能力，不只是日志回调；PreToolUse/PostToolUse/Stop/compact 等事件可作为治理入口。
- 长任务需要 thread store、compaction、rollout/budget、多 agent 状态，而不是只靠 prompt。

## Caveats

Rust monorepo 学习成本高，不适合作为最小实现模板。它更适合作为“生产边界检查清单”：如果我们要构建自己的 harness，可以用它验证哪些问题迟早会出现。

## Suggested Next Reading

优先继续读 `core/src/session/turn.rs`、`core/src/session/turn_context.rs`、`core/src/agent/control.rs`、`sandboxing/src/windows.rs`，再横向对照 OpenHands SDK 的 Python 实现。
