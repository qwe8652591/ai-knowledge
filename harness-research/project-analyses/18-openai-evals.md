# OpenAI Evals

Local source: `../../repos/openai-evals`

## What It Is

OpenAI Evals 是 OpenAI 较早的 eval framework/registry，用于评估 LLM 或 LLM-based systems。README 现在提示可在 OpenAI Dashboard 直接配置/运行 Evals，但本仓仍有 registry、Eval/Solver、completion functions、model-graded evals 等源码参考。

## Harness Architecture

核心模型：

`registry YAML/data -> Eval class -> samples -> completion_fn or Solver -> record events -> aggregate metrics`

`SolverEval` 支持每个 sample 拷贝一个 stateful Solver，避免样本间状态污染。

## Key Source Entry Points

- `evals/eval.py`: `Eval`、`SolverEval`，样本打乱/并发、recorder、per-sample rng、gentle interrupt。
- `evals/solvers/solver.py`: `Solver`、`SolverResult`、postprocessors、NestedSolver、动态创建 solver。
- `evals/registry.py`: registry 解析。
- `evals/completion_fns/`: OpenAI/LangChain/solver completion function。
- `evals/elsuite/modelgraded/`: model-graded eval templates。
- `evals/registry/evals/`、`evals/registry/modelgraded/`: YAML registry。

## Useful Ideas For Our Harness

- Solver 作为“被测系统”包装层，比直接绑模型 API 灵活。
- 每个样本复制 solver，避免 state leakage。
- postprocessor pipeline 可以记录每一步输入/输出，方便调试评分。
- registry + YAML 模板能降低非工程用户编写 eval 的门槛。

## Caveats

这是较早期框架，历史包袱和 LFS registry 数据较多；现代 eval harness 设计可优先参考 Inspect AI。OpenAI Evals 适合补充 registry/solver/model-graded 模式。

## Suggested Next Reading

读 `docs/eval-templates.md`、`docs/completion-fns.md`，再选一个 `modelgraded` YAML 看低代码 eval 如何落地。
