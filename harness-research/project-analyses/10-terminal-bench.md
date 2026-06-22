# Terminal-Bench

Local source: `../../repos/terminal-bench`

## What It Is

Terminal-Bench 是面向真实终端环境的 agent benchmark。它包含任务数据集和执行 harness：agent 在 Docker/tmux terminal 中完成任务，harness 运行测试脚本并解析结果。

## Harness Architecture

核心链路：

`Dataset tasks -> Harness -> per-task TrialHandler -> DockerCompose Terminal -> tmux session -> Agent.perform_task -> run-tests.sh -> parser -> TrialResults/BenchmarkResults`

Terminal-Bench 同时有 agent adapters，例如 Claude Code、Codex、Gemini CLI、OpenHands、Aider、Goose 等。

## Key Source Entry Points

- `terminal_bench/harness/harness.py`: 主 harness。处理 dataset、agent factory、resume lock、并发 trial、run metadata、测试运行、结果聚合。
- `terminal_bench/agents/base_agent.py`: `BaseAgent.perform_task(instruction, session, logging_dir)` contract。
- `terminal_bench/terminal/terminal.py`: Docker Compose + tmux session 生命周期。
- `terminal_bench/parsers/`: pytest/SWE-bench/MLE-bench 等结果解析器。
- `terminal_bench/agents/installed_agents/`: 不同外部 CLI agent 的 adapter。

## Useful Ideas For Our Harness

- 真实终端 benchmark 可以把 agent interaction 限定在 tmux session 中，便于录制与复盘。
- agent adapter contract 要足够薄：输入 instruction/session，输出 token/failure/timestamps。
- run lock + per-task result 是可恢复大规模 benchmark 的关键。
- 测试 parser 需要和 task 类型解耦。

## Caveats

README 已建议新用户看 Harbor。Terminal-Bench 的旧 harness 对 Docker/tmux 假设较重，通用云环境、multi-step、网络策略等抽象不如 Harbor 完整。

## Suggested Next Reading

读 `terminal_bench/harness/harness.py` 的 `_run_trial()` 和 `agents/installed_agents/codex/codex_agent.py`，理解如何接入真实 CLI agent。
