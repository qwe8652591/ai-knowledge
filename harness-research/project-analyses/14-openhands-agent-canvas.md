# OpenHands Agent Canvas

Local source: `../../repos/openhands`

## What It Is

当前下载到的 OpenHands 仓库是 Agent Canvas/control center：自托管的 coding agents 与 automations 控制台，可连接本地、Docker、VM、Cloud/Enterprise backend，也可运行 OpenHands、Claude Code、Codex、Gemini 或 ACP-compatible agents。

README 明确说明真正的 OpenHands Agent/Agent Server 源码已迁到 `OpenHands/software-agent-sdk`，Agent Canvas 源码迁到 `OpenHands/agent-canvas`。

## Harness Architecture

这里更像产品层：

`Agent Canvas UI -> one/multiple Agent Server backends -> agents/conversations/automations -> Slack/GitHub/Linear/etc integrations`

它关注用户如何启动、切换、监控和自动化 agents，而不是 agent loop 本身。

## Key Source Entry Points

- `README.md`: 当前项目定位、quickstart、architecture、迁移说明。
- `docs/SELF_HOSTING.md` 等文档：部署和安全硬化。
- 对 runtime 源码分析应转到 `../../repos/software-agent-sdk`。

## Useful Ideas For Our Harness

- 生产形态通常会分成 agent runtime/server 和 control center/UI 两层。
- 一个 UI 可以连接多个 backend，允许 local/personal/team/cloud agent 并存。
- automations 是 harness 产品化后的自然延伸：schedule/webhook/Slack/GitHub 触发 agent。
- ACP-compatible agent 支持说明协议层会越来越重要。

## Caveats

本仓不是最佳源码分析对象。对于 runtime、安全、工具和 workspace，请以 Software Agent SDK 为准。

## Suggested Next Reading

把本项目作为产品架构参考，源码深挖转向 [OpenHands Software Agent SDK](15-openhands-software-agent-sdk.md)。
