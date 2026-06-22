# Agent Harness 实践模式

本文件聚焦“如何构建一个可靠的 agent harness”。这些实践来自 OpenAI Codex、ChatGPT Workspace Agents、OpenAI Agents SDK、Anthropic Claude Agent SDK、Claude Code hooks / permissions，以及开源 agent 框架的共同模式。

## 典型架构

一个生产级 agent harness 可以先按这些模块拆：

- Goal intake：接收用户目标，澄清范围、约束和验收条件。
- Instruction layer：系统指令、组织规则、项目规则、任务上下文。
- Context builder：选择相关文件、文档、历史、memory、检索结果。
- Agent loop：推理、行动、观察、更新计划、继续或停止。
- Tool registry：定义可用工具、参数 schema、返回结构、失败模式。
- Permission gate：按风险决定 allow、ask、deny 或 require human review。
- Sandbox / environment：限制文件系统、网络、命令、凭据、外部副作用。
- State and checkpoint：保存计划、进度、工件、决策、可恢复上下文。
- Hooks / guardrails：在关键生命周期节点拦截、验证、记录或拒绝动作。
- Tracing：记录模型输出、工具调用、handoff、guardrail、错误和 side effect。
- Eval loop：从 trace 和失败案例构建 dataset、grader 和回归测试。
- Human oversight：让人可以审批、纠偏、接管、复盘。

## 实践 1：把上下文当成代码管理

不要把所有知识塞进一个超长 prompt。更好的做法是让 instruction 文件变成项目的一部分：

- 用 `AGENTS.md` 或类似文件存放项目级 agent 指南。
- 把架构、运行命令、测试策略、质量标准放到 `docs/`。
- 指令文件只保留目录和关键规则，细节用链接组织。
- 让 agent 能在需要时按路径读取，而不是一次性吞下所有内容。
- 把计划、假设、重要决策和技术债写成可追踪文档。

判断标准：新人或 agent 只读这些文档，就能知道如何搭建、修改、测试和提交。

## 实践 2：工具设计比提示词更关键

工具是 agent 接触世界的边界。工具设计不好，模型能力再强也会表现不稳定。

建议：

- 每个工具有清晰 schema、用途、限制和失败返回。
- 工具返回结构化数据，不返回难解析的大段自然语言。
- 高风险工具拆成 prepare / preview / execute 两阶段。
- 文件写入、命令执行、网络访问、凭据访问分开授权。
- 对外部系统写操作默认需要审批或 dry-run。
- 避免一个万能工具拥有过宽权限。

## 实践 3：权限要细，不要只有“全开/全关”

Anthropic Claude Code 的 permissions 模式和 OpenAI Codex 的 sandbox / approvals 都体现了同一个方向：降低 approval fatigue，同时把真正危险的动作拦下来。

可以参考的权限分层：

- Read：读取本地文件、查看状态、检索文档。
- Local write：修改工作区文件。
- Local execute：运行项目命令、测试、lint。
- Network read：访问公网或内部只读 API。
- Network write：调用会产生外部副作用的 API。
- Secrets：读取 token、密钥、生产配置。
- Destructive：删除、重置、迁移、覆盖、发布、付款、发消息。

默认策略：

- 低风险、可回滚、限定在 workspace 内的动作可以自动执行。
- 高风险、跨边界、不可回滚、有外部副作用的动作需要 human review。
- 生产环境和真实用户数据操作需要更严格的 policy。

## 实践 4：用 hook 做确定性控制

LLM 适合推理和规划，不适合承担所有安全判断。hook / guardrail 适合做确定性检查：

- PreToolUse：检查工具名、参数、路径、命令、URL、风险等级。
- PostToolUse：记录结果、转换日志、检测异常。
- PermissionRequest：把风险动作交给人或 policy engine。
- Stop / Finalize：验证是否真的满足验收条件。
- PreCompact：在上下文压缩前保存关键状态。

hook 应该尽量短、小、确定性强。不要把所有策略写成另一个大模型调用。

## 实践 5：长任务需要状态机

长时间运行的 agent 容易出现三类问题：做太多、过早宣布完成、测试浅尝辄止。

对应的 harness 设计：

- 把大目标拆成可验证 milestone。
- 每个 milestone 有明确完成条件。
- 保存 todo、decision log、open questions、blocked reasons。
- 每轮结束前运行 evaluator 或 checklist。
- 需要 context compaction 时保留目标、约束、已完成、未完成、证据。
- 恢复任务时先读状态，而不是重新猜测上下文。

## 实践 6：Trace 是 eval 的原材料

OpenAI Agents SDK 的 tracing 把模型生成、工具调用、handoff、guardrail 和 custom events 记录下来。实践上，trace 是 agent 质量改进的入口：

1. 先收集真实任务 trace。
2. 标注失败类型：理解错、工具选错、权限拦截、上下文缺失、验证不足、过早停止。
3. 把典型失败转成 eval dataset。
4. 写 scorer / grader 判断是否复现或修复。
5. 再改 prompt、tool、context、guardrail 或 harness。

不要只改 prompt 后跑一个例子就认为修复了。

## 实践 7：评测要覆盖 harness，不只覆盖模型

Agent 失败可能来自模型，也可能来自 harness：

- 模型不会计划。
- harness 没给够上下文。
- 工具 schema 不清楚。
- 工具返回不可解析。
- 权限策略阻断合理动作。
- sandbox 缺依赖。
- evaluator 太弱。
- trace 没记录关键事件。

因此 eval 需要能定位失败层级，而不是只给一个最终分数。

## 反模式

- 把 harness 理解成一段超级系统提示词。
- 所有工具都塞进一个 `run_shell(command)`。
- 默认跳过权限，依赖模型“自觉安全”。
- 没有 trace，只看最终答案。
- 没有回归 eval，每次凭感觉调 prompt。
- 长任务没有 checkpoint，context 一压缩就丢状态。
- 测试只跑 happy path，不跑失败、权限、网络、恢复和误用场景。
- 让 agent 直接接触生产密钥和生产数据。

## 最小可行 harness

如果要自己做一个最小原型，可以从这些能力开始：

1. 一个 agent loop：`think -> choose tool -> execute -> observe -> continue/stop`。
2. 一个 typed tool registry。
3. 一个 permission policy：allow / ask / deny。
4. 一个 workspace sandbox。
5. 一个 append-only trace log。
6. 一个 task state 文件。
7. 一个 eval runner，能重放 5 到 20 个代表性任务。

先把这七件事做可靠，比一开始做多 agent 更有价值。
