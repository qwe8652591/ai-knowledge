# Google ADK Python

Local source: `../../repos/google-adk-python`

## What It Is

Google Agent Development Kit 2.0 是 code-first Python agent framework，重点是构建、评估、部署复杂 AI agents。2.0 强调 Workflow runtime 和 Task API，覆盖 agents、workflow graph、sessions、artifacts、plugins、tools、code executors、telemetry、optimization。

## Harness Architecture

应用主线：

`Agent/Workflow/App -> Runner -> InvocationContext/session/events -> model/tools/code executors/plugins -> Event stream -> session/artifact/memory services`

Workflow runtime 支持 deterministic graph flows：routing、fan-out/fan-in、loops、retry、state、dynamic nodes、HITL、nested workflows。

## Key Source Entry Points

- `src/google/adk/agents/base_agent.py`: agent tree、sub_agents、before/after callbacks、agent state、event creation。
- `src/google/adk/agents/llm_agent.py`: LLM agent 具体能力。
- `src/google/adk/runners.py`: `Runner`，管理 App/agent/node、session service、artifact service、memory、credential、plugins、run events。
- `src/google/adk/workflow/`: graph/node runner、parallel worker、retry、dynamic node、join node。
- `src/google/adk/tools/`: base tool、agent tool、bash tool、skill toolset、tool confirmation。
- `src/google/adk/code_executors/`: local/container/GKE/Vertex/Agent Engine sandbox code executors。
- `src/google/adk/sessions/`、`artifacts/`、`plugins/`、`telemetry/`: 生产应用配套。

## Useful Ideas For Our Harness

- Agent 和 Workflow 统一到 App/Runner 是通用应用框架的好形态。
- session/artifact/memory/credential service 都应从一开始接口化。
- Workflow graph 适合确定性业务流程，agent loop 适合开放式任务；两者需要能互相嵌套。
- callbacks/plugins/telemetry 是企业应用必须的扩展点。

## Caveats

ADK 是通用框架，不是专门 coding agent harness。它提供的 tool/code executor/sandbox 需要针对 coding 场景再补权限、repo/workspace、patch、tests、review 等语义。

## Suggested Next Reading

读 `runners.py` 的 event/session 处理，再读 `workflow/_workflow.py` 和 `tools/agent_tool.py`，理解 Task API/Workflow 如何组织多 agent。
