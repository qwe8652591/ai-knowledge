# 源码阅读地图

下面是后续深入源码时的优先路径。目标不是把每个文件都看完，而是先找到 harness 的控制点。

## 优先级 1：OpenClaw Code Agent

本地路径：`repos/emerging-hot-projects/openclaw-code-agent`

最值得看：

- `src/harness/`：Claude Code、Codex、OpenCode 后端 adapter。
- `src/session-manager.ts`、`src/session.ts`、`src/session-store.ts`：session 生命周期和持久化。
- `src/session-plan-approval-delivery.ts`、`src/plan-review-summary.ts`：计划审批。
- `src/worktree.ts`、`src/worktree-merge.ts`、`src/worktree-pr.ts`、`src/worktree-lifecycle.ts`：worktree、merge、PR、状态机。
- `src/goal-controller.ts`、`src/goal-store.ts`：长期 goal loop。
- `src/tools/agent-launch.ts`、`src/tools/agent-merge.ts`、`src/tools/agent-pr.ts`、`src/tools/goal-launch.ts`：暴露给 OpenClaw agent 的工具层。
- `docs/ARCHITECTURE.md`、`docs/REFERENCE.md`、`docs/ACP-COMPARISON.md`：设计边界。

阅读问题：

- 它如何把不同 coding CLI 的输出统一成 session event？
- plan approval 是如何阻塞和恢复同一个 session 的？
- worktree 何时创建、何时可清理、何时保留 PR？
- OpenClaw chat route/thread metadata 如何一路带到完成通知？

## 优先级 2：OpenClaw 本体

本地路径：`repos/emerging-hot-projects/openclaw`

最值得看：

- `packages/gateway-protocol/src/schema/`：agent、sessions、tools、plugins、exec approvals、cron、channels 等协议 schema。
- `packages/plugin-sdk/`：插件如何注册工具和能力。
- `src/tui/`、`ui/src/ui/controllers/`、`ui/src/ui/views/`：session/agent UI 和控制器。
- `extensions/`：官方插件和 channel/plugin 例子。
- `packages/tool-call-repair/`：tool call 修复和 normalizer。
- `docs/`：architecture、gateway、sandboxing、security、tools、skills。

阅读问题：

- Gateway 把 channel message、agent session、tool call 和 plugin event 如何串起来？
- sandbox mode 对 main/non-main session 的边界是什么？
- plugin 的权限、安装、声明工具 allowlist 如何处理？
- sessions tool 如何实现跨 session 路由？

## 优先级 3：OpenShell + NemoClaw

本地路径：

- `repos/emerging-hot-projects/openshell`
- `repos/emerging-hot-projects/nemo-claw`

OpenShell 最值得看：

- `architecture/gateway.md`、`architecture/sandbox.md`、`architecture/security-policy.md`
- `crates/openshell-core/src/policy.rs`、`crates/openshell-core/src/inference.rs`
- `crates/openshell-server/src/policy_store.rs`、`crates/openshell-server/src/inference.rs`
- `crates/openshell-supervisor-process/src/sandbox/linux/landlock.rs`
- `crates/openshell-supervisor-process/src/sandbox/linux/seccomp.rs`
- `crates/openshell-supervisor-network/src/l7/`
- `crates/openshell-providers/src/providers/`
- `examples/sandbox-policy-quickstart/`
- `examples/agent-driven-policy-management/`

NemoClaw 最值得看：

- `src/lib/sandbox/`：sandbox 配置、stream、channels、command support。
- `src/lib/policy/`：policy tiers、context、failure classifier。
- `src/lib/inference/`：provider models、Ollama/vLLM/web search、runtime context。
- `src/lib/security/`：redaction、credential filter、secret patterns。
- `nemoclaw/src/blueprint/`：blueprint runner/state/snapshot/private network/SSRF。
- `docs/network-policy/`、`docs/security/`、`docs/manage-sandboxes/`。

阅读问题：

- policy 在 host、gateway、sandbox supervisor、network proxy 之间如何流动？
- credential 如何注入 agent，又如何避免泄漏到 sandbox filesystem？
- inference routing 如何把 agent 的本地请求转发到受控 provider？
- NemoClaw 是如何把 OpenClaw/Hermes 的安装和运行变成 blueprint lifecycle 的？

## 优先级 4：Bernstein

本地路径：`repos/emerging-hot-projects/bernstein`

注意：Windows checkout 有长路径报错，核心源码已可读。如要完整跑测试，建议 WSL/Linux 或启用 Windows long paths 后重新 clone。

最值得看：

- `src/bernstein/core/worktrees/`：worktree isolation。
- `src/bernstein/core/workflows/`：workflow spec 和 runner。
- `docs/security/audit-log.md`、`docs/security/manager-auth.md`、`docs/security/credential-scoping.md`
- `docs/eval/yaml-harness.md`
- `docs/diagrams/agent_lifecycle.md`
- `tests/unit/test_lineage_*`、`tests/unit/test_policy_*`、`tests/unit/test_merge_queue.py`
- `tests/unit/test_scheduler_partitions.py`、`tests/unit/test_schedule_audit_verify.py`

阅读问题：

- deterministic scheduler 如何避免 LLM 参与调度？
- audit chain 和 artifact lineage 如何建模？
- merge queue 与 worktree isolation 如何配合？
- policy/harness/eval tests 如何覆盖生产风险？

## 优先级 5：Dexto

本地路径：`repos/emerging-hot-projects/dexto`

最值得看：

- `docs/docs/concepts/`：agent、tools、agents vs workflows。
- `docs/docs/guides/configuring-dexto/agent-yml.md`
- `docs/docs/guides/configuring-dexto/permissions.md`
- `docs/docs/guides/configuring-dexto/sessions.md`
- `docs/docs/mcp/`
- `packages/webui/lib/stores/sessionStore.ts`
- `packages/webui/lib/stores/approvalStore.ts`
- `packages/tools-process/src/`
- `packages/tools-scheduler/src/`
- `packages/tools-todo/src/`

阅读问题：

- YAML agent config 如何映射成 runtime？
- approval store 和 permission mode 如何影响 tool call？
- session/memory/MCP 之间如何组织？
- coding agent 与通用 agent 是否共享同一 harness？

## 优先级 6：Parallel Code / ccswarm / Claw Orchestrator

### Parallel Code

本地路径：`repos/emerging-hot-projects/parallel-code`

看：

- task/worktree 创建逻辑；
- diff review 和 merge UI；
- Docker sandbox 支持；
- agent CLI spawning 和 settings。

适合研究“用户操作台”。

### ccswarm

本地路径：`repos/emerging-hot-projects/ccswarm`

看：

- `crates/ccswarm/src/workflow/`
- `crates/ccswarm/src/providers/`
- `crates/ccswarm/src/session/bridge.rs`
- `crates/ccswarm/src/events/`
- `crates/ccswarm/src/governance/`
- `crates/ccswarm/src/cli/handlers/harness.rs`
- `crates/ai-session/src/`

适合研究“声明式 flow + provider adapter + audit trail”。

### Claw Orchestrator

本地路径：`repos/emerging-hot-projects/claw-orchestrator`

看：

- `src/session-manager.ts`
- `src/persistent-session.ts`
- `src/persistent-codex-session.ts`
- `src/persistent-claude-session.ts`（如存在）
- `src/persistent-gemini-session.ts`
- `src/council.ts`
- `src/autoloop/`
- `src/openai-compat.ts`
- `skills/references/`

适合研究“如何把多个 coding CLI 统一成 programmable sessions”。

## 可跳过或后置

- `awesome-openclaw-skills`、`awesome-openclaw-agents`：先作为生态样本和技能/agent 模板目录，不必一开始深挖每个条目。
- `agency-agents-zh`：适合研究角色/团队/中文市场适配，不是 harness 核心实现。

