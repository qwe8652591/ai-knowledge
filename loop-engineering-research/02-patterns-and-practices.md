# 模式与实践

## 常见模式

### 1. Ralph Loop

核心形态：

```bash
while true; do
  cat PROMPT.md | claude
done
```

真正有价值的不是 while 循环本身，而是它周围的状态和验证：

- `PROMPT.md` 定义每轮行为；
- `AGENTS.md` 定义项目规则和命令；
- `IMPLEMENTATION_PLAN.md` 或 task JSON 作为跨轮状态；
- 每轮 fresh context，读取同一组文件；
- 每轮做一个任务，跑测试，commit，更新计划；
- 下一轮从更新后的计划继续。

适合：长 refactor、大量小任务、持续修复测试、从 PRD 执行到工作代码。

风险：没有 sandbox 时危险；没有强测试时会“看似推进”；没有预算和停止条件时容易成本失控。

### 2. Goal Continuation Loop

代表：OpenAI Codex `/goal`、`claude-goal`。

核心是 persistent objective：

- 用户设定目标；
- agent 每轮工作；
- Stop hook 或内建 goal engine 在轮次结束时判断是否继续；
- 若未完成，注入 continuation prompt；
- 状态存入 goal store；
- 支持 pause/resume/clear/budget/status；
- 完成前由 evaluator 或验证器确认。

适合：比一个 prompt 大、比开放 backlog 小的目标。

### 3. Evaluator-Optimizer Loop

代表：Anthropic building effective agents、OpenAI Agents SDK orchestration/evals。

结构：

```text
generator -> evaluator -> feedback -> generator
```

适合有明确评价标准的任务：代码质量、测试通过、文档覆盖、生成物评分、benchmarks、UI 检查、模型输出质量。

关键是 evaluator 不能只会“夸/骂”，要能给可执行反馈，最好能调用工具读取真实状态。

### 4. Worktree Loop

每个 loop 或 checkpoint 在独立 branch/worktree 里执行，主分支保持干净。

适合：

- 多 agent 并行；
- 尝试多个方案；
- 夜间自动开发；
- PR/CI gate。

关键设计：

- worktree 生命周期；
- branch 命名；
- dirty state 保护；
- merge/PR 策略；
- 冲突升级；
- cleanup safety。

### 5. Multi-Agent / Team Loop

主 agent 不做所有事，而是调度 subagents：

- planner：拆任务；
- researcher：读代码/文档；
- coder：改代码；
- reviewer：审查；
- tester/verifier：跑测试；
- security：查风险。

这能降低主上下文污染，但会增加成本和协调复杂度。

### 6. Memory-Survival Loop

代表：Elves、multi-agent-ralph-loop。

核心是把长任务记忆拆成多层：

- Plan：权威任务范围；
- Survival Guide：compact/restart 后第一读取；
- Execution Log：每批次执行记录；
- Learnings：可复用经验；
- `.ai-docs` 或项目规则：稳定知识沉淀。

适合长时间无人值守和跨上下文运行。

### 7. Scored Improvement Loop

代表：Codex difficult-problems、autoresearch。

每轮修改后运行 evaluator 得分，只保留改进。

适合：

- prompt 优化；
- ML/搜索/benchmark；
- UI Lighthouse 分数；
- bundle size；
- 测试覆盖率；
- fuzz/regression。

## Loop 的基本组件

| 组件 | 作用 | 常见实现 |
| --- | --- | --- |
| Objective | 明确目标和边界 | `/goal`、`GOAL.md`、issue、PRD |
| State | 跨轮共享事实 | `PLAN.md`、SQLite、JSON、PR comments |
| Scheduler | 决定下一步 | bash loop、Stop hook、workflow engine、human |
| Worker | 执行任务 | Claude Code、Codex、OpenCode、subagents |
| Verifier | 判断是否对 | tests、lint、typecheck、LLM judge、reviewer |
| Budget | 防止无限跑 | token/turn/time/cost caps |
| Safety | 限制损害 | sandbox、worktree、denylist、permissions |
| Observability | 让人可审查 | logs、traces、run report、commits、PR |
| Handoff | 停止后交接 | summary、run report、blocker report |

## 安全实践

- 不要在 host 上给无人值守 loop 开 `bypassPermissions`，除非有 sandbox。
- secrets、SSH keys、browser cookies、cloud credentials 要隔离。
- 默认从 report-only 或 draft-only 开始，再逐步到 assisted fix、auto PR、auto merge。
- 高风险操作必须有 allowlist/denylist。
- 每个 loop 要有 kill switch。
- 长 loop 要有预算。
- merge 应该由 PR/CI/review gate 决定，不应只信 agent 自述。

## 实施 checklist

1. 目标是否比一个 prompt 大、但比开放 backlog 小？
2. 完成条件是否可验证？
3. 每轮最多做什么？
4. 每轮读哪些持久状态？
5. 每轮写哪些持久状态？
6. 失败反馈写到哪里？
7. 测试/验证命令是什么？
8. 没有进展几轮后如何判定 blocked？
9. token/turn/time/cost 上限是什么？
10. 是否在 worktree/sandbox 中运行？
11. 是否有日志、commit、PR 或 report 可审计？
12. 人在何处介入：approve、review、merge、stop、resume？

## 反模式

- 只写一个无限 while loop，没有停止条件。
- 用自然语言“你自己判断完成”代替可执行 verifier。
- 把所有历史都塞进上下文，不维护外部状态。
- 让多个 loop 同时修改同一个 checkout。
- 无人值守 loop 拿到完整本机权限。
- 没有成本预算。
- 没有失败摘要，第二天只能读几万行日志。

