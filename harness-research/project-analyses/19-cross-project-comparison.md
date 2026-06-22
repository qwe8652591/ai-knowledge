# Cross-Project Comparison

这份文档横向比较已下载的 18 个 harness 相关项目：它们的优劣、适用场景，以及为什么会采用这样的设计。

## One-Sentence Conclusion

这些项目没有“唯一最优”。它们大体分成三条主线：生产 agent runtime、评测/benchmark harness、通用 agent framework。设计差异主要来自目标不同：是让 agent 安全地干活，还是让模型/agent 可复现地被评测，还是让开发者快速搭应用。

## Big Picture

| 类别 | 代表项目 | 核心问题 | 设计重心 |
| --- | --- | --- | --- |
| 生产 coding agent harness | OpenAI Codex、OpenHands Software Agent SDK、DeepAgents、OpenHarness | 如何让模型在真实代码环境里安全、持续、可恢复地行动 | tool loop、permissions、sandbox、context、hooks、state、UI/server |
| CLI runtime 的 SDK 控制层 | Claude Agent SDK Python/TypeScript、Claude Agent SDK Demos | 如何把已有成熟 CLI agent 嵌入应用 | subprocess/control protocol、streaming、callbacks、session store、UI glue |
| 评测框架 | Inspect AI、OpenAI Evals、lm-evaluation-harness | 如何稳定地定义任务、调用模型、评分、记录日志 | Task/Solver/Scorer、registry、metrics、logs、model adapters |
| agent benchmark runner | SWE-bench、Terminal-Bench、Harbor | 如何在真实环境里可复现地跑 agent/patch 并判定结果 | Docker/env、agent adapters、verifier、trial/job、resume、artifacts |
| 通用 agent/multi-agent framework | Google ADK、Microsoft AutoGen | 如何构建可组合 agent 应用和多 agent workflow | Runner、Agent tree、workflow graph、message runtime、plugins、tools |
| 长任务实践 primitives | CWC Long Running Agents | 如何防止长任务自我欺骗和断点丢失 | default-fail、fresh evaluator、handoff、hooks |

## Best Project By Need

| 你要解决的问题 | 最值得参考 | 原因 |
| --- | --- | --- |
| 做一个生产级本地 coding agent | OpenAI Codex、OpenHands SDK | Codex 边界最完整；OpenHands SDK 的 Python 分层更易复用 |
| 做一个可组合 Python agent harness | DeepAgents | middleware/backends 设计清楚，站在 LangGraph 上少造底层轮子 |
| 做一个最小可读 harness 原型 | OpenHarness | Python 实现直接，tool loop/permission/query flow 容易读 |
| 把 Claude Code 能力嵌入产品 | Claude Agent SDK Python + Demos | SDK 控制面和 WebSocket/UI session 模式完整 |
| 设计 eval harness | Inspect AI | Task/Solver/Scorer/log 分层最清晰 |
| 评测 coding patch 是否修好 issue | SWE-bench | Docker/TestSpec/fail-to-pass/pass-to-pass 是行业标准式设计 |
| 评测 terminal agents 或多 agent benchmarks | Harbor | Job/Trial/Environment/Agent/Verifier 抽象最通用 |
| 研究多 agent 消息运行时 | AutoGen | runtime/topic/envelope/team patterns 经典 |
| 做企业 agent workflow 应用 | Google ADK | App/Runner/Workflow/session/artifact/plugin 一体化 |

## Production Agent Harness Comparison

| 项目 | 优势 | 劣势 | 为什么这样设计 |
| --- | --- | --- | --- |
| OpenAI Codex | 生产边界最完整；Rust crates 把 session、tool、sandbox、permissions、hooks、MCP、多 agent 拆得细；适合真实本地代码执行 | 复杂度高，学习曲线陡；不适合直接当最小模板 | 它要跑在用户本机，执行 shell/file/git 等高风险操作，所以必须把 sandbox、approval、hooks、state 做成硬边界 |
| OpenHands Software Agent SDK | Python API 清晰；Conversation/Agent/Workspace/Tool/Event/Server 分层好；本地/远程 workspace 统一；工具并发和资源锁考虑充分 | 代码量大，server、workspace、tool、event 概念多；初学者容易迷路 | 它既要支持 SDK，又要支撑 Agent Server/Cloud/UI，所以必须服务化、事件化、workspace 抽象化 |
| DeepAgents | middleware/backends 组合漂亮；filesystem、shell、skills、memory、subagents、summarization、HITL 默认集成；适合 LangChain 生态 | 依赖 LangGraph/LangChain；若只想要极简 loop，会显得重 | 它的目标是“开箱即用但可替换”，所以把能力做成 middleware，把执行环境做成 backend |
| OpenHarness | Python 代码可读，便于学习；tool registry、permission checker、query loop、MCP、swarm 都能看到；适合实验 | 成熟度和安全审计不如大厂/成熟项目；部分设计更像教学和社区实践 | 它要展示 harness 全貌并快速迭代，所以选择 Python 单仓实现，牺牲部分严格边界换取可理解性 |

### Takeaway

如果我们要自建 harness，建议学习顺序是：OpenHarness 看最小主路径，DeepAgents 学可组合架构，OpenHands SDK 学 Python 生产分层，Codex 学安全边界和本地 runtime 的极限复杂度。

## Claude SDK Family Comparison

| 项目 | 优势 | 劣势 | 为什么这样设计 |
| --- | --- | --- | --- |
| Claude Agent SDK Python | 直接复用 Claude Code CLI 能力；stream-json/control protocol、permission callbacks、hooks、MCP、session store 适合产品嵌入 | 真实 runtime 在 CLI 内，源码不能完全展示底层 agent loop | Anthropic 要保证 SDK 与 Claude Code 行为一致，所以 SDK 做控制面，不重复实现 runtime |
| Claude Agent SDK TypeScript | session store examples 和 changelog 有价值；适合 TS 产品持久化参考 | 本地源码只有少量 examples，没有核心 SDK runtime | TS 包更像发布包/薄仓库，本地 repo 重点放在 reference adapters，而不是暴露全部实现 |
| Claude Agent SDK Demos | 展示 WebSocket streaming、session queue、resume、UI tool/result 渲染 | demo 不是生产架构；安全/权限不是重点 | 目标是教开发者把 SDK 变成应用，所以设计重心在产品胶水层 |

### Takeaway

Claude 系列最值得学的是“不要重复造 runtime”。如果已有一个强 CLI agent，可以用 SDK 包一层控制协议、session store、callbacks 和 UI adapter。

## Evaluation Harness Comparison

| 项目 | 优势 | 劣势 | 为什么这样设计 |
| --- | --- | --- | --- |
| Inspect AI | Task/Solver/Scorer 分层干净；支持 tools、sandbox、logs/viewer；适合现代 eval harness | 不是 coding runtime；产品化 agent 权限/文件操作不是主线 | 安全研究和评测需要可复现、可审计、可扩展任务，所以把任务、求解器、评分器彻底拆开 |
| OpenAI Evals | registry/YAML/model-graded evals 经验丰富；SolverEval per-sample copy 防状态污染 | 历史包袱较重；现代使用上不如 Inspect AI 清晰 | 早期目标是让社区贡献 eval 和让 OpenAI 比较模型，所以 registry 和模板优先 |
| lm-evaluation-harness | 学术 benchmark 成熟；模型 adapter 多；task/metric/cache/bootstrapping 强 | 主要评模型，不评真实 agent 行动；工具/沙箱不是重点 | 它服务 leaderboard 和论文复现，因此重点是模型后端、任务标准化、指标聚合 |
| Inspect Evals | 大量 Inspect AI eval 示例；社区贡献/注册流程值得学 | 不是 runtime，本质是 eval asset layer | eval 数量增长后，主问题变成组织、文档、依赖和贡献流程 |

### Takeaway

要做 eval harness，Inspect AI 是最好骨架；OpenAI Evals 提供 registry/model-graded 的历史经验；lm-eval 适合基础模型能力评测。

## Benchmark Runner Comparison

| 项目 | 优势 | 劣势 | 为什么这样设计 |
| --- | --- | --- | --- |
| SWE-bench | patch evaluation 极强；Docker/TestSpec/log parser/fail-to-pass/pass-to-pass 判定清楚 | 评最终 patch，不看 agent 过程；任务形态集中在 GitHub issue 修复 | 它要回答“这个 patch 是否修好了真实 issue”，所以一切围绕可复现测试和 resolved 判定 |
| Terminal-Bench | 真实 terminal 环境；Docker/tmux/asciinema 便于记录；agent adapters 多 | 旧 harness 抽象较窄，Docker/tmux 假设重 | 它要评估“agent 会不会用终端完成任务”，所以 tmux session 是核心交互界面 |
| Harbor | Job/Trial/Environment/Agent/Verifier 抽象现代；支持云环境、网络策略、multi-step、RL rollouts | 复杂度较高，理解成本高 | 它要成为通用 agent benchmark 平台，所以必须把 agent、environment、verifier、trial lifecycle 全部接口化 |

### Takeaway

SWE-bench 是“代码补丁判定”的标杆；Terminal-Bench 是“终端任务交互”的标杆；Harbor 是“通用 agent benchmark runner”的下一代抽象。

## General Agent Framework Comparison

| 项目 | 优势 | 劣势 | 为什么这样设计 |
| --- | --- | --- | --- |
| Google ADK Python | Runner/App/Workflow/session/artifact/plugin 体系完整；workflow graph 适合企业应用 | 不专注 coding harness；真实 repo/sandbox/patch/test 语义要自己补 | Google 要支持多种 agent app 和部署场景，所以选择 workflow runtime + service abstractions |
| Microsoft AutoGen | 多 agent runtime、topic/message envelope、AgentChat teams 经典；适合理解多 agent 编排 | 已进入 maintenance mode；新项目要谨慎；coding harness 能力不完整 | AutoGen 起源于多 agent 研究/应用原型，所以重心是 message runtime 和 team patterns |

### Takeaway

ADK/AutoGen 更适合做“agent 应用框架”，不是直接做 coding agent harness。它们的 orchestration 思路有价值，但安全执行和 benchmark 还要参考 Codex/OpenHands/Harbor。

## Why The Designs Differ

### 1. Runtime vs SDK

Codex、OpenHands SDK、DeepAgents 自己负责 agent loop，因此必须处理工具执行、状态、权限、上下文、恢复。Claude SDK 复用 Claude Code CLI，所以设计成控制协议和进程包装。

结论：如果 runtime 已经强，就做 SDK；如果要掌控边界，就做完整 runtime。

### 2. Local Safety vs Cloud Scale

Codex 强调本地 sandbox/approval，因为它直接碰用户机器。Harbor 强调 environment provider、network policy、resource policy，因为它要并发跑很多隔离环境。

结论：本地 agent 的核心是用户安全；benchmark 平台的核心是环境一致性和规模化。

### 3. Agent Work vs Evaluation Work

Agent harness 要优化行动链路：工具、权限、上下文、恢复。Evaluation harness 要优化判定链路：dataset、runner、scorer、metrics、logs。

结论：不要把 eval 写进 agent loop，也不要让 scorer 依赖 agent 的自述。

### 4. Minimal Readability vs Production Boundaries

OpenHarness 可读，适合学习；Codex/OpenHands SDK 复杂，适合生产。复杂不是偶然，而是权限、并发、恢复、跨平台、UI/server、可观测性都会把 harness 拉复杂。

结论：原型可以小，但生产边界迟早要补。

### 5. Framework vs Product

DeepAgents/ADK/AutoGen 更像开发框架；OpenHands Agent Canvas 和 Claude demos 更像产品层。框架强调扩展点，产品层强调 session、UI、backend、automation 和人机协作。

结论：自建时要分清我们是在做 core harness，还是做用户操作台。

## Strengths And Weaknesses By Dimension

| 维度 | 最强项目 | 原因 | 主要短板 |
| --- | --- | --- | --- |
| 本地 coding runtime | OpenAI Codex | sandbox/approval/hooks/session/tool handler 完整 | Rust 复杂度高 |
| Python coding SDK | OpenHands SDK | Conversation/Workspace/Tool/Event 分层完整 | 概念多 |
| 可组合 agent harness | DeepAgents | middleware/backends 设计自然 | 依赖 LangChain 生态 |
| 最易读原型 | OpenHarness | Python 主路径直观 | 成熟度待验证 |
| Claude 产品嵌入 | Claude SDK Python/Demos | CLI parity、streaming、session resume | 底层 runtime 不透明 |
| eval framework | Inspect AI | Task/Solver/Scorer/log 清楚 | 非 coding 产品 runtime |
| patch benchmark | SWE-bench | Docker/TestSpec/grading 标准化 | 不看过程 |
| terminal benchmark | Terminal-Bench | Docker/tmux 真实交互 | 通用性不如 Harbor |
| 通用 benchmark runner | Harbor | Job/Trial/Env/Agent/Verifier 抽象完整 | 学习成本高 |
| 多 agent runtime | AutoGen | message runtime/team patterns 经典 | 维护模式 |
| 企业 workflow | Google ADK | Workflow/App/Runner/service 体系 | coding 语义不足 |

## What We Should Borrow

1. 从 Codex 借：sandbox/approval/hooks/tool handler/thread state 的硬边界。
2. 从 Claude SDK 借：CLI runtime + SDK control plane + session store 的产品嵌入方式。
3. 从 DeepAgents 借：middleware/backend 化的 harness 组合方式。
4. 从 OpenHands SDK 借：Conversation/Workspace/Tool/Event 分层和 resource locking。
5. 从 Inspect AI 借：Task/Solver/Scorer/log 的 eval 分层。
6. 从 SWE-bench 借：TestSpec、Docker cache、fail-to-pass/pass-to-pass grading。
7. 从 Harbor 借：Job/Trial/Environment/Agent/Verifier 通用 benchmark 生命周期。
8. 从 CWC 借：default-fail、fresh-context evaluator、handoff contract。

## Suggested Architecture For Our Own Harness

如果我们后面要自建一个 harness，建议不要一开始追求全能，而是分三层：

### Core Agent Runtime

- `Conversation/Thread`
- `Turn`
- `ContextBuilder`
- `ToolRegistry`
- `ToolExecutor`
- `PermissionGate`
- `Sandbox/Workspace`
- `HookBus`
- `EventLog`
- `Compactor/Resume`

### Eval Layer

- `Task`
- `AgentAdapter`
- `Environment`
- `Verifier/Scorer`
- `Metric`
- `TraceReplay`
- `Report`

### Product Layer

- CLI/TUI
- WebSocket event stream
- Session store
- Project/worktree manager
- Automation triggers
- Dashboard/log viewer

这样可以避免把所有复杂度塞进一个 “Agent” 类，也能让后续评测和产品化自然长出来。
