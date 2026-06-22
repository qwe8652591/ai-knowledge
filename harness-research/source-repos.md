# 本地源码索引

源码统一放在工作区根目录的 `repos/` 下。大部分仓库以 GitHub zip 快照下载，两个 Claude Agent SDK 仓库是浅克隆。zip 快照没有 `.git` 历史，但足够做架构和源码分析。

机器可读下载记录见 [repo-download-manifest.json](repo-download-manifest.json)。

## 已下载项目

| 优先级 | 项目 | 本地路径 | 文件数 | 下载方式 | 研究重点 |
| --- | --- | --- | ---: | --- | --- |
| P0 | OpenAI Codex | `../repos/openai-codex` | 5140 | zip | Codex agent loop、sandbox、approval、tool execution、app/server protocol |
| P0 | Claude Agent SDK Python | `../repos/claude-agent-sdk-python` | 128 | git shallow | SDK API、permission callbacks、hooks、sessions、query loop |
| P0 | Claude Agent SDK Demos | `../repos/claude-agent-sdk-demos` | 269 | zip | email agent、research agent、Excel demo、端到端应用 harness |
| P0 | Anthropic CWC Long-Running Agents | `../repos/cwc-long-running-agents` | 12 | zip | default-fail contract、fresh-context evaluator、handoff hooks |
| P0 | Inspect AI | `../repos/inspect_ai` | 1911 | zip | Task / Solver / Scorer / Tool / Sandbox / Log 模型 |
| P0 | SWE-bench | `../repos/swe-bench` | 660 | zip | coding-agent benchmark、repo setup、patch validation |
| P0 | LangChain Deep Agents | `../repos/deepagents` | 980 | zip | planning tool、virtual filesystem、subagents、human-in-loop |
| P1 | OpenAI Evals | `../repos/openai-evals` | 1728 | zip | eval registry、solver、grader、model providers |
| P1 | Claude Agent SDK TypeScript | `../repos/claude-agent-sdk-typescript` | 33 | git shallow | TS SDK API、query/options 类型设计 |
| P1 | Inspect Evals | `../repos/inspect_evals` | 3839 | zip | eval examples、agent/coding/security benchmark patterns |
| P1 | lm-evaluation-harness | `../repos/lm-evaluation-harness` | 16219 | zip | model eval task registry、adapter、metric runner |
| P1 | Terminal-Bench | `../repos/terminal-bench` | 2474 | zip | terminal task protocol、containerized agent benchmark |
| P1 | Harbor | `../repos/harbor` | 2746 | zip | agent benchmark orchestration、environment/runtime abstraction |
| P1 | OpenHarness | `../repos/openharness` | 479 | zip | community agent harness、tools、memory、permission、hooks |
| P1 | OpenHands | `../repos/openhands` | 2440 | zip | coding agent platform、controller、runtime、sandbox |
| P1 | OpenHands Software Agent SDK | `../repos/software-agent-sdk` | 1339 | zip | workspace abstraction、agent server、coding-agent SDK |
| P2 | Google ADK Python | `../repos/google-adk-python` | 2156 | zip | agent framework、tools、multi-agent、eval/deploy |
| P2 | Microsoft AutoGen | `../repos/microsoft-autogen` | 1837 | zip | multi-agent patterns、legacy AutoGen abstraction |

## 第一轮源码阅读路线

### 1. Agent harness 主线

先读：

- `../repos/openai-codex/README.md`
- `../repos/openai-codex/codex-rs/README.md`
- `../repos/openai-codex/codex-rs/core/`
- `../repos/openai-codex/codex-rs/exec/`
- `../repos/openai-codex/codex-rs/app-server-protocol/`
- `../repos/claude-agent-sdk-python/src/`
- `../repos/claude-agent-sdk-demos/email-agent/ccsdk/`
- `../repos/cwc-long-running-agents/README.md`
- `../repos/cwc-long-running-agents/claude-code-config/`

重点问题：

- agent loop 在哪里启动？
- 每轮模型输出如何转成 tool call？
- tool 执行结果如何回写上下文？
- 权限审批如何进入控制流？
- hook / guardrail 在生命周期的哪个节点运行？
- 长任务如何保存状态和恢复？

### 2. Evaluation harness 主线

先读：

- `../repos/inspect_ai/src/inspect_ai/`
- `../repos/inspect_ai/examples/`
- `../repos/inspect_evals/src/inspect_evals/`
- `../repos/swe-bench/swebench/`
- `../repos/lm-evaluation-harness/lm_eval/`
- `../repos/terminal-bench/`

重点问题：

- task / dataset / solver / scorer 如何分层？
- model adapter 和 agent adapter 如何接入？
- sandbox / docker / environment 如何复现？
- scoring 是测试、规则、LLM judge，还是混合？
- trace/log 记录哪些信息，能否回放？

### 3. Framework 对比主线

先读：

- `../repos/deepagents/src/`
- `../repos/openharness/`
- `../repos/openhands/openhands/`
- `../repos/software-agent-sdk/`
- `../repos/google-adk-python/src/`
- `../repos/microsoft-autogen/python/`

重点问题：

- 框架把 agent 抽象成 class、graph、loop、server，还是 workflow？
- 工具注册和上下文管理在哪一层？
- 是否支持多 agent、handoff、memory、human-in-the-loop？
- 是否内置 eval、trace、sandbox、permission？

## 下载备注

- Git clone 在 `openai-evals` 上会触发 Git LFS 大文件下载，容易卡住；本地采用 zip 快照规避。
- `openai-codex` 有较长 snapshot 文件名，PowerShell `Expand-Archive` 会遇到路径问题；本地采用 `tar --strip-components=1` 解压。
- `software-agent-sdk` 的正确仓库为 `https://github.com/OpenHands/software-agent-sdk`。
- zip 快照没有 commit hash。若后续需要精确复现实验，可对某个仓库重新拉取指定 commit 的 archive。
