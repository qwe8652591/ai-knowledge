# lm-evaluation-harness

Local source: `../../repos/lm-evaluation-harness`

## What It Is

EleutherAI 的 lm-evaluation-harness 是经典语言模型评测框架，面向 academic benchmark、model adapters、prompt/config、metrics、request cache。它主要评测模型输出能力，不是 agent 行动能力。

## Harness Architecture

核心链路：

`CLI/simple_evaluate -> model adapter -> TaskManager/tasks -> build requests -> LM loglikelihood/generate -> filters/postprocess -> metrics/aggregation -> results`

它支持 HF/vLLM/API 等模型后端，以及 YAML-config tasks、few-shot、chat template、request cache、bootstrap stderr。

## Key Source Entry Points

- `lm_eval/evaluator.py`: `simple_evaluate()`，模型初始化、task 选择、seed、cache、chat template、结果聚合。
- `lm_eval/api/task.py`: `Task` 抽象，dataset docs、fewshot、doc_to_text/target、output type、metrics。
- `lm_eval/api/model.py`: LM adapter 接口。
- `lm_eval/tasks/`: 大量 task config 与实现。
- `lm_eval/api/registry.py`: model/task/metric registry。

## Useful Ideas For Our Harness

- model adapter 应与 task/metric 解耦，方便同一 task 横向比较模型。
- task YAML 化能降低新增 benchmark 的门槛。
- request cache 对大规模评测非常关键。
- metrics aggregation 和 stderr/bootstrap 是严肃 benchmark 报告必须考虑的部分。

## Caveats

它基本不处理工具调用、沙箱、长会话、真实文件系统任务。适合做“模型基础能力评测”的参考，而不是 agent harness 主参考。

## Suggested Next Reading

读 `docs/config_files.md` 与几个 YAML task，提炼轻量 task registry 设计。
