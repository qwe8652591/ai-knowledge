# SWE-bench

Local source: `../../repos/swe-bench`

## What It Is

SWE-bench 是 coding benchmark harness：给定真实 GitHub issue 对应的 repo/base commit/test patch，模型提交 patch，harness 在 Docker 环境中应用 patch、运行测试、解析结果，判断是否 resolved。

## Harness Architecture

核心执行链路：

`dataset instance + prediction patch -> TestSpec -> build/reuse Docker images -> apply patch -> run eval.sh -> parse test logs -> fail-to-pass/pass-to-pass -> report.json`

它的关键在可复现环境和判分规则，而不是 agent loop。

## Key Source Entry Points

- `swebench/harness/run_evaluation.py`: 主执行器。加载 predictions/dataset，构建 containers，复制 patch，尝试 `git apply`/`patch`，运行 eval script，写 logs/report。
- `swebench/harness/test_spec/test_spec.py`: `TestSpec`，把 dataset instance 转成 repo/env/eval scripts 和 Docker image keys。
- `swebench/harness/grading.py`: 解析测试日志，计算 `FAIL_TO_PASS`、`PASS_TO_PASS`，决定 resolved。
- `swebench/harness/docker_build.py`、`docker_utils.py`: image/container 生命周期。
- `swebench/harness/log_parsers/`: 各 repo 测试输出解析器。

## Useful Ideas For Our Harness

- benchmark harness 应把 environment build、prediction apply、test run、grading 分成可缓存步骤。
- resolved 判断不能只看所有测试通过；SWE-bench 用 fail-to-pass 衡量修复，用 pass-to-pass 防回归。
- 每个实例应有独立 log dir，包括 patch、eval.sh、test_output、report，方便复盘。
- Docker image key 应包含 env script/spec hash，避免错误复用。

## Caveats

它评的是最终 patch，不评 agent 过程。若要研究交互式 coding agent 行为，需要和 Terminal-Bench/Harbor/Codex 结合。

## Suggested Next Reading

继续读 `test_spec/create_scripts.py` 和各 repo `log_parsers`，理解 benchmark 如何从数据构造可执行测试。
