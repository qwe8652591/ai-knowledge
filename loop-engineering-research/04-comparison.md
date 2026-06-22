# 项目比较

## 横向对比

| 项目 | Loop 控制方式 | 状态载体 | 验证方式 | 适合场景 | 主要风险 |
| --- | --- | --- | --- | --- | --- |
| loop-engineering | pattern/starter/audit 工具 | `LOOP.md`、STATE、registry | loop-audit、checklist | 建立 loop 方法论和模板 | 更像参考框架，不是单一 runtime |
| Ralph Playbook | bash 外层循环 | `PROMPT.md`、`AGENTS.md`、specs、plan | tests/build/commit | 理解 Ralph 本质 | 需要使用者自己补安全与预算 |
| ralph-loop | 脚本迭代 | `.agent/tasks.json`、task files、logs | tests/lint/typecheck/screenshot | 可运行长任务 loop | 对项目测试质量依赖高 |
| Ralph Desktop | 桌面 UI + CLI execution | 项目 task/spec/state | completion signal、CLI output | 新手使用 Ralph、多 CLI 控制 | UI 状态与真实 repo 状态一致性 |
| claude-goal | Claude Code Stop hook continuation | SQLite + transcript + MCP | evaluator subagent + worker self-audit | Claude Code goal loop | hook/SQLite/Claude 版本耦合 |
| oh-my-claudecode | skills + team pipeline | OMC runtime state、skills、teams | team-verify/team-fix | Claude Code 多 agent 编排 | 功能多，复杂度高 |
| Elves | staged run + survival loop | Plan、Survival Guide、Learnings、Execution Log、PR | tests、review、PR/CI、reports | 夜间多批次开发 | 需要强项目验证和人类 merge |
| multi-agent-ralph-loop | Claude Agent Teams + hooks | MemPalace layers、rules、vault | 4-stage quality gates | 复杂多 agent 项目 | 规则/记忆系统可能过重 |
| agentic-ai-engineering | 教学式 agent loop | Python examples | eval harness tutorials | 学习 loop 基础 | 不是生产系统 |
| OpenAI Codex `/goal` | 内建 persistent goal | Codex goal state | user-defined validation、status controls | Codex 长任务 | 完成条件写不好会漂移 |
| Anthropic hooks/subagents | hooks + subagent verifier | transcript、settings、project files | prompt/agent hooks | Claude Code lifecycle enforcement | hook 副作用和配置治理 |
| LangGraph | state graph / durable workflow | graph state/checkpoint | node-level validators | 自建 resilient agents | 框架学习成本 |
| SWE-agent | action-observation loop | issue/env/trajectory | tests/patch outcomes | SWE benchmark/bug fix | 不等同于通用 loop engineering |

## 设计取舍

### 简单循环 vs 状态机

Ralph 的优点是简单：bash loop、prompt、plan file。它让人马上理解 loop 的本质。缺点是预算、恢复、安全、观测、并发都要自己补。

Claude Goal、Codex Goal 和 LangGraph 更接近状态机：目标、状态、继续/暂停/恢复/完成都有明确模型。它们更可靠，但需要更多工程。

### Fresh context vs 长上下文

Ralph 派强调 fresh context：每轮重新读关键文件，避免上下文腐烂。Elves 进一步把 survival guide 和 execution log 设计成可恢复记忆。

长上下文适合探索和理解，但长 loop 的可靠性通常来自外部状态，而不是把所有历史塞进上下文。

### Verifier 是工具还是模型

强 verifier 应该优先使用真实工具：tests、lint、typecheck、CI、screenshot diff、静态分析。LLM judge 适合主观质量或审查，但最好能读取真实文件和命令结果。

`claude-goal` 的 evaluator subagent 是一个好方向：不是只看 worker 自述，而是用工具验证。

### 单 agent vs 多 agent

多 agent 能并行探索、分离 maker/checker、保持主上下文清洁。但成本、调度、冲突和状态一致性会变复杂。

适合多 agent 的条件：

- 任务能拆；
- 有共享状态；
- 有独立验证；
- 有合并策略；
- 有预算。

### 自动 merge vs 人类 gate

大多数项目都建议：loop 可以自动推进、自动开 PR、自动修复 review，但最终 merge 最好保留人类 gate 或 CI/review 强 gate。Loop 的最佳位置是“把工作推进到可 review 状态”，不是绕过工程控制。

## 优先研究顺序

1. `loop-engineering`：先建立术语、pattern、checklist。
2. `how-to-ralph-wiggum` + `ralph-loop`：理解最小 loop 如何工作。
3. `claude-goal`：研究 Stop hook、SQLite、MCP、evaluator subagent 这种工程化 goal loop。
4. `Elves`：研究 long-run memory survival 和 staged/launch 分离。
5. `oh-my-claudecode`：研究 Claude Code 多 agent team/autopilot。
6. `multi-agent-ralph-loop`：研究 hooks、quality gates、memory palace。
7. `agentic-ai-engineering`：补 first-principles agent loop/eval 基础。

## 对自建 Loop Harness 的建议

最小版本不要一开始做“大而全多 agent”。建议路线：

1. 先做 single-agent loop：`GOAL.md` + `PLAN.md` + verifier + run log。
2. 加 budget：max turns、max wall time、max token/cost。
3. 加 worktree：每个 goal 独立分支。
4. 加 completion evaluator：用工具验证，而不是信自述。
5. 加 recovery：resume/blocked/abandon。
6. 加 PR flow：自动开 PR，不自动 merge。
7. 最后再加 subagents 和 parallel workers。

