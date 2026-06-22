# Inspect AI

Local source: `../../repos/inspect_ai`

## What It Is

Inspect AI 是 UK AI Security Institute 的 LLM evaluation harness。它不是 coding agent runtime，而是通用评测框架，支持 prompt engineering、tool use、多轮对话、model-graded evals、sandbox、logs/viewer。

## Harness Architecture

核心抽象非常干净：

`Task(dataset/setup/solver/scorer/sandbox/limits) -> Solver or Agent -> Generate/tool loop -> Scorer/metrics -> Eval log/viewer`

Task 负责评测配置，Solver 负责把 TaskState 转成模型行为，Scorer 负责把最终状态和 target 变成 Score，logs 负责复盘。

## Key Source Entry Points

- `src/inspect_ai/_eval/task/task.py`: `Task` 参数最集中，包含 dataset、solver、scorer、sandbox、limits、approval、epochs、error policy。
- `src/inspect_ai/solver/_solver.py`: `Generate` 和 `Solver` protocol，agent/eval loop 的可组合接口。
- `src/inspect_ai/scorer/_scorer.py`: `Scorer` protocol、metrics registry。
- `src/inspect_ai/solver/_basic_agent.py`: ReAct-style `basic_agent`，展示 tool loop、submit tool、message/token limits、scorer feedback。
- `src/inspect_ai/tool/`、`src/inspect_ai/approval/`、`src/inspect_ai/log/`: 工具、审批、日志。

## Useful Ideas For Our Harness

- eval harness 应将“被测策略”和“评分器”彻底分离。
- `TaskState` 是所有 solver/scorer 共享的稳定接口，减少框架耦合。
- limits、sandbox、approval 应是 Task 级配置，而非散落在 solver 内。
- logs/viewer 是评测框架的一等产物，不是调试附属品。

## Caveats

Inspect AI 适合评测，不直接解决生产 coding agent 的权限、文件编辑、人机协作、长会话恢复等问题。它和 Codex/DeepAgents 是互补关系。

## Suggested Next Reading

重点读 `basic_agent` 和 `Task` 配置解析，再看 `inspect_evals` 中具体 eval 如何落地。
