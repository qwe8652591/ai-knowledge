# CWC Long Running Agents

Local source: `../../repos/cwc-long-running-agents`

## What It Is

这是一个小而精的 long-running agent primitives 仓库，核心不是框架，而是几条可移植的长任务治理原则：default-fail、fresh-context evaluator、agent-maintained handoff。

## Harness Architecture

它围绕 Claude Code/Agent SDK hooks/subagent 展示长任务闭环：

`goal/spec -> generator agent works -> evidence gate -> fresh evaluator checks -> handoff notes/commit -> next session resumes`

`verify-gate.sh` 是教学性质的 PreToolUse gate，`evaluator.md` 是隔离上下文、无写权限的 evaluator agent 规范。

## Key Source Entry Points

- `README.md`: 三个 primitives 的定义与意图。
- `evaluator.md`: skeptical reviewer，限制工具为 Read/Glob/Grep/Bash 中安全子集，输出 `PASS` 或 `NEEDS_WORK`。
- `verify-gate.sh`: default-fail/evidence-read gate 示例。

## Useful Ideas For Our Harness

- 评估标准默认都应为 false，不能让 agent 直接宣称完成。
- evaluator 应该新上下文、只读、独立判断，避免被 generator 的叙述污染。
- handoff note 应由 agent 持续维护，包含进度、证据、下一步，而不是结束时补写。
- hooks 可以实现“行为契约”，例如写测试结果前必须读证据。

## Caveats

`verify-gate.sh` 自己也说明它不是安全边界，有 basename-only、Bash bypass 等问题。借鉴的是模式，不是原样使用脚本。

## Suggested Next Reading

把这里的三条 primitive 抽成我们自己的 long-running harness checklist：evidence ledger、fresh evaluator、handoff contract。
