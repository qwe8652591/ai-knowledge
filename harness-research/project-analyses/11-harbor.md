# Harbor

Local source: `../../repos/harbor`

## What It Is

Harbor 是 Terminal-Bench 团队的新一代 agent/model evaluation and optimization framework。它能运行 Terminal-Bench 2.0、SWE-Bench、Aider Polyglot 等，并支持 Docker、Daytona、Modal、LangSmith、Blaxel、Novita 等环境，目标还包括 RL rollouts。

## Harness Architecture

Harbor 的抽象很完整：

`JobConfig -> Job -> TrialQueue -> Trial(single/multi-step) -> Environment + Agent + Verifier -> TrialResult -> Metrics/JobResult`

环境、agent、verifier 都是接口化的；Trial 负责生命周期、hooks、日志、artifact、network policy、timeouts、skills 注入、agent setup/run、verification。

## Key Source Entry Points

- `src/harbor/job.py`: Job 是一组 trials 的入口，负责 config、task resolving、metrics、cache、resume、progress、TrialQueue。
- `src/harbor/trial/trial.py`: Trial 生命周期基类，负责 environment start/stop、agent setup/run、verification、logs、artifacts、network policy。
- `src/harbor/trial/single_step.py`、`multi_step.py`: 不同 workload shape。
- `src/harbor/environments/base.py`: 环境接口，资源限制、网络策略、exec/upload/download/service ops。
- `src/harbor/agents/base.py`: agent adapter contract，`setup()` 与 `run()` 分离。
- `src/harbor/verifier/base.py`: verifier contract。
- `src/harbor/agents/installed/`: Claude Code/Codex/OpenHands 等 agent adapters。

## Useful Ideas For Our Harness

- benchmark runner 应把 `Agent`、`Environment`、`Verifier` 作为三根主轴，而不是把逻辑写进任务脚本。
- agent setup 和 agent run 分离，可以缓存/复用环境并清楚归因失败。
- network policy 是评测可信度的一部分，应支持 public/no-network/allowlist 等。
- multi-step trial、artifact collect hooks、separate verifier environment 都是严肃 agent benchmark 的必要能力。
- Job/Trial result 要能 resume，并支持 pass@k、metrics aggregation。

## Caveats

Harbor 抽象多、依赖环境复杂，学习成本高。若只做单一 benchmark，SWE-bench 更直接；若做通用平台，Harbor 更值得深入。

## Suggested Next Reading

读 `trial/single_step.py`、`trial/multi_step.py` 和一个具体 installed agent，比如 `agents/installed/codex.py`，再看 `models/task/config.py` 的 task format。
