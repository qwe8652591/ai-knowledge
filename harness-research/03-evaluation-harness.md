# Evaluation Harness 研究笔记

Evaluation harness 的目标是让模型、agent 或完整系统在同一任务协议下运行，并产生可复现、可比较、可追踪的结果。

## 基本组成

一个 evaluation harness 通常包含：

- Dataset：任务输入、参考答案、测试仓库、环境描述或人工标注样本。
- Task protocol：模型或 agent 看到什么、允许做什么、最终输出什么。
- Adapter：把不同模型、agent CLI、SDK 或服务接入同一 runner。
- Environment：本地进程、Docker、Kubernetes、浏览器、文件系统或远程沙箱。
- Solver / runner：执行任务，管理轮次、工具、超时、资源和失败。
- Scorer / grader：自动测试、规则打分、LLM-as-judge、人工评分或组合评分。
- Logs / traces：完整记录输入、工具调用、输出、环境状态和错误。
- Reproducibility：固定版本、镜像、seed、依赖、配置、成本和时间限制。

## 评测对象

### Model eval

评测单次或少轮模型能力，例如知识、推理、分类、生成质量。EleutherAI `lm-evaluation-harness` 是代表项目。

### System eval

评测一个 LLM 应用，例如 RAG、客服 bot、文档问答、结构化抽取。OpenAI Evals、DeepEval、promptfoo 等更常见。

### Agent eval

评测 agent 是否能在环境里完成任务，例如修复 GitHub issue、操作终端、运行测试、使用浏览器、调用工具。SWE-bench、Terminal-Bench、Inspect AI 更相关。

### Harness eval

评测 agent 外壳本身是否可靠。例如：

- 上下文选择是否足够。
- 工具 schema 是否让模型正确调用。
- 权限策略是否拦住危险动作。
- sandbox 是否可复现。
- hook 是否记录和拦截关键事件。
- agent 是否能恢复长任务。

这一类通常需要自定义 eval，因为公开 benchmark 很少直接衡量 harness 设计。

## 指标设计

可先从这些指标入手：

- Task success：任务是否完成。
- Test pass rate：自动测试是否通过。
- Regression rate：旧任务是否被新 harness 改坏。
- Tool efficiency：工具调用次数、无效调用比例、重复调用比例。
- Cost and latency：token、工具时间、总运行时间。
- Permission friction：审批次数、误拦截、漏拦截。
- Unsafe action blocked：危险动作是否被 policy 拦住。
- Recovery success：中断或压缩后能否继续。
- Trace completeness：是否记录足够信息用于复盘。
- Human intervention quality：人介入后 agent 是否能正确继续。

## 典型项目怎么定位

### EleutherAI lm-evaluation-harness

最经典的模型评测 harness。适合研究 task 配置、模型 adapter、大规模 benchmark 运行和结果管理。

### OpenAI Evals

面向 LLM 或 LLM system 的 eval framework，适合研究自定义 eval、私有数据评测和注册式 eval 组织方式。

### Inspect AI

更贴近现代 agent eval。它支持 coding、agentic tasks、reasoning、knowledge、behavior、多模态等评测，并提供 tools、multi-turn dialog、model-graded evals 等能力。适合研究 dataset -> solver -> scorer 的清晰结构。

### SWE-bench

面向真实 GitHub issue 的软件工程评测。核心思想是给定 codebase 和 issue，让模型生成 patch，再用测试判断是否解决。适合研究 coding-agent 的环境复现和自动评分。

### Terminal-Bench / Harbor

面向真实终端任务的 agent benchmark。相比单纯代码 patch，它更强调 agent 在终端环境中完成端到端任务。适合研究 shell agent、containerized environments 和长任务评估。

## 对 agent harness 的实验建议

### 实验 1：同任务多 harness 对比

选 10 个小型 coding tasks，用四种配置跑：

- prompt-only
- prompt + tools
- agent loop + permission gate
- agent loop + permission gate + trace/eval feedback

比较完成率、工具调用、成本、审批次数和失败类型。

### 实验 2：权限策略压测

设计 20 个工具调用场景：

- 读文件
- 改工作区文件
- 删除文件
- 访问网络
- 发送消息
- 读取 secret
- 修改生产配置

检查 policy 是否正确 allow / ask / deny，并记录误判。

### 实验 3：上下文缺失测试

同一个任务分别给：

- 无项目文档
- 只有 README
- README + AGENTS.md
- README + AGENTS.md + docs/architecture + test commands

比较 agent 的定位速度、修改正确率和测试行为。

### 实验 4：恢复能力测试

让 agent 进行一个 5 到 10 步任务，中途模拟：

- context compaction
- 进程重启
- tool failure
- 测试失败
- 人工改动文件

观察是否能读取 state、识别已完成和未完成事项，并继续工作。

## 评测文件建议

如果后续在本 repo 里做实验，可以建立：

```text
evals/
  datasets/
  runners/
  scorers/
  traces/
  reports/
```

每次实验至少保存：

- harness config
- model config
- tool config
- permissions config
- task input
- full trace
- final artifact
- scorer output
- human notes
