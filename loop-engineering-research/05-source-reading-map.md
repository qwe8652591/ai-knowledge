# 源码阅读地图

## 1. cobusgreyling/loop-engineering

本地路径：`repos/loop-engineering/loop-engineering`

优先看：

- `docs/primitives.md`
- `docs/primitives-matrix.md`
- `docs/loop-design-checklist.md`
- `docs/operating-loops.md`
- `docs/failure-modes.md`
- `patterns/`
- `starters/minimal-loop*/`
- `tools/loop-audit/src/`
- `tools/loop-init/src/`
- `tools/loop-cost/src/`

阅读问题：

- 一个 loop readiness score 如何定义？
- starter 如何把 Claude/Codex/Grok 的能力映射成同一类 loop？
- patterns 如何区分 report-only、assisted、unattended？

## 2. Ralph Playbook / Ralph Loop

本地路径：

- `repos/loop-engineering/how-to-ralph-wiggum`
- `repos/loop-engineering/ralph-loop`

优先看：

- `how-to-ralph-wiggum/files/loop.sh`
- `how-to-ralph-wiggum/files/PROMPT_plan.md`
- `how-to-ralph-wiggum/files/PROMPT_build.md`
- `how-to-ralph-wiggum/files/AGENTS.md`
- `ralph-loop/ralph.sh`
- `ralph-loop/scripts/lib/`
- `ralph-loop/.agent/` 或模板目录（如存在）

阅读问题：

- 每轮如何选择任务？
- 何处强制测试和 commit？
- task status 如何保存？
- Docker sandbox 和 agent CLI 如何对接？

## 3. claude-goal

本地路径：`repos/loop-engineering/claude-goal`

优先看：

- `scripts/stop.sh`
- `scripts/post-tool-batch.sh`
- `scripts/session-start.sh`
- `prompts/continuation.md`
- `prompts/evaluator.md`
- `mcp/goal-server/src/`
- `mcp/goal-server/src/tools/`
- `mcp/goal-server/src/migrations/`
- `tests/integration/continuation-loop.bats`
- `tests/integration/completion-detection.bats`
- `tests/integration/budget-exhaustion.bats`

阅读问题：

- Stop hook 如何把下一轮 continuation 注入 Claude Code？
- token accounting 如何从 transcript JSONL 提取？
- SQLite goal lifecycle 有哪些状态？
- evaluator subagent 如何避免 worker 自述造假？

## 4. Ralph Desktop

本地路径：`repos/loop-engineering/ralph-desktop`

优先看：

- `src/lib/stores/loop*`
- `src/lib/services/loopStart*`
- `src/routes/`
- `src-tauri/`
- `scripts/e2e/`
- `AGENTS.md`

阅读问题：

- UI 如何表示 loop state、pause/resume/stop？
- 如何抽象 Claude Code、Codex、OpenCode？
- completion signal 如何检测？

## 5. Elves

本地路径：`repos/loop-engineering/elves`

优先看：

- `SKILL.md`
- `references/autonomy-guide.md`
- `references/verification-patterns.md`
- `references/plan-template.md`
- `references/survival-guide-template.md`
- `references/execution-log-template.md`
- `references/learnings-template.md`
- `references/council-workflow.md`
- `references/codex-goals.md`
- `scripts/preflight_worktree.py`
- `scripts/workspace_guard.py`

阅读问题：

- Stage 和 launch 为什么要分两步？
- Survival Guide / Learnings / Execution Log 分别承担什么？
- PR loop 如何读取 review/CI 并继续修复？
- Cobbler 如何做 read-only quick decision 和 full run coordination？

## 6. oh-my-claudecode

本地路径：`repos/loop-engineering/oh-my-claudecode`

优先看：

- `docs/REFERENCE.md`
- `docs/TEAM-WORKTREE-MODE.md`
- `tests/integration/concurrent-ralph-sessions.test.ts`
- `tests/integration/concurrent-project-memory.test.ts`
- skill/agent/team/autopilot 相关目录

阅读问题：

- `/autopilot`、`/ralph`、`/team` 的边界是什么？
- Team pipeline 如何 plan -> prd -> exec -> verify -> fix？
- tmux CLI workers 与 in-session native team 有何不同？

## 7. multi-agent-ralph-loop

本地路径：`repos/loop-engineering/multi-agent-ralph-loop`

优先看：

- `.claude/skills/`
- `.claude/rules/`
- hooks/scripts
- `tests/`
- `docs/architecture/`

阅读问题：

- MemPalace 4 层记忆如何筛选规则？
- quality gates 如何阻止 task 完成？
- Agent Teams 如何并行执行？
- 自动学习 pipeline 如何避免噪音？

## 8. agentic-ai-engineering

本地路径：`repos/loop-engineering/agentic-ai-engineering`

优先看：

- `01-foundations/05-agent-loop/`
- `02-effective-agents/05-evaluator-optimizer/`
- `02-effective-agents/06-human-in-the-loop/`
- `04-testing-evaluation/`
- `05-frameworks/02-langgraph/`

阅读问题：

- 最小 agent loop 如何从 tool call 发展出来？
- evaluator-optimizer 与 Ralph/Goal 的区别是什么？
- eval harness 如何成为 loop 的 feedback signal？

## 9. OpenAI Cookbook archive / LangGraph / SWE-agent

本轮状态：

- `openai-cookbook` archive zip 已下载到 `repos/loop-engineering/_tmp/openai-cookbook.zip`，未完整解压。
- `langgraph` 和 `SWE-agent` 未下载完整源码，使用远端 metadata 与官方文档/README 级别分析。

后续如要继续深挖：

- OpenAI Cookbook：重点找 Codex goals、agent improvement loop、evals/tracing examples。
- LangGraph：重点看 checkpoint、state graph、interrupt/human-in-loop、durable execution。
- SWE-agent：重点看 agent-environment interface、trajectory、action/observation loop、SWE-bench integration。

