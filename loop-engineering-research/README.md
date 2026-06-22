# Loop Engineering Research

研究日期：2026-06-22  
范围：研究 loop engineering 的定义、实践模式、代表项目和源码结构。重点关注 Claude Code、Codex、Ralph Loop、goal mode、hooks、subagents、worktrees、eval/trace feedback loop。

## 本目录文件

- [01-definitions.md](01-definitions.md)：loop engineering 的定义、边界，以及和 harness/context/eval engineering 的关系。
- [02-patterns-and-practices.md](02-patterns-and-practices.md)：常见 loop 模式、设计要素、风险和落地 checklist。
- [03-project-catalog.md](03-project-catalog.md)：项目目录、热度信号、本地下载状态。
- [04-comparison.md](04-comparison.md)：项目横向比较、优劣、设计原因。
- [05-source-reading-map.md](05-source-reading-map.md)：源码深挖路线。
- [source-repos.md](source-repos.md)：本轮下载的源码快照和远端 commit。
- [repo-download-manifest.json](repo-download-manifest.json)：机器可读下载清单。
- [sources.md](sources.md)：网页和源码来源。

源码/快照位置：

```text
repos/loop-engineering/
```

## 最短结论

Loop engineering 是从“人不断提示 agent”转向“人设计一个会持续提示、检查、纠偏、恢复、升级和停止 agent 的系统”。它不是单个 prompt，也不等于 agent harness；更准确地说，它是运行在 harness 之上的反馈控制层。

一个实用的 loop 至少包含：

- objective：目标和边界；
- state：磁盘文件、数据库、PR、issue、run log、survival guide；
- iteration：每轮如何选择任务、执行、验证、记录；
- verifier：测试、lint、typecheck、screenshot、LLM judge、review subagent；
- stop condition：完成、预算耗尽、阻塞、需要人类、风险升级；
- recovery：compact/restart/resume 后如何恢复；
- safety：sandbox、权限、denylist、secrets、network、worktree；
- observability：日志、trace、成本、状态、PR/commit 链路。

## 分类

| 类别 | 代表项目/能力 | 重点 |
| --- | --- | --- |
| Minimal Ralph loop | how-to-ralph-wiggum, ralph-loop | while loop + prompt file + plan file + fresh context |
| Goal continuation loop | Codex `/goal`, claude-goal | persistent objective + stop hook/continuation + budget/status/evaluator |
| Loop design system | cobusgreyling/loop-engineering | primitives、patterns、audit、cost、starter kits |
| Visual/user-facing loop controller | Ralph Desktop | 把 brainstorm、task spec、loop execution、resume 做成桌面控制台 |
| Multi-agent loop | oh-my-claudecode, multi-agent-ralph-loop, Elves | subagents/teams、parallel workers、review/fix loop、memory stack |
| Eval/scored improvement loop | OpenAI Codex difficult-problems, OpenAI Agents SDK tracing/evals, Anthropic evaluator-optimizer | 用分数、trace、eval、judge 让 agent 可持续改进 |
| Foundation agent loop | agentic-ai-engineering, LangGraph, SWE-agent | 从 LLM/tool loop 到图状态机、SWE issue loop |

## 对我们后续自建系统的启发

如果把前一轮 harness 研究和这轮 loop engineering 合起来，一个可靠的 coding agent 系统应该分三层：

1. Harness 层：模型、工具、权限、sandbox、session、trace。
2. Loop 层：目标、状态、迭代、验证、预算、恢复、停止。
3. SDLC 层：worktree、PR、CI、review、merge、release、audit。

最小可行原型可以从 Ralph/Goal 混合方案开始：

- `GOAL.md` 定义目标、范围、停止条件；
- `PLAN.md` 作为跨轮状态；
- 每轮 fresh context，读取 `GOAL.md` + `PLAN.md` + `AGENTS.md`；
- 每轮只做一个 checkpoint；
- 每轮必须跑 verifier；
- 成功更新 plan 并 commit；
- 失败记录 blocker；
- 达到完成条件或预算上限时停下。

