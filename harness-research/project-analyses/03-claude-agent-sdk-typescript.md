# Claude Agent SDK TypeScript

Local source: `../../repos/claude-agent-sdk-typescript`

## What It Is

本地副本主要包含 README、CHANGELOG 和 session-store reference adapters，没有 Python SDK 那样的核心 `src/` runtime。它仍然有参考价值：README 说明 TS SDK 目标是用 Claude Code 能力构建 autonomous agents；CHANGELOG 暴露了 SDK/CLI 同步能力；examples 展示了 session persistence contract。

## Harness Architecture

从本地源码能看到的重点是持久化/恢复层，而不是 agent loop：

`query({ options: { sessionStore } }) -> SessionStore append/load/delete -> external backend`

examples 包含 S3、Redis、Postgres 三类 adapter，并给了 conformance suite，说明 SDK 把 transcript/session persistence 抽象为可替换后端。

## Key Source Entry Points

- `README.md`: SDK 定位、安装和迁移说明。
- `CHANGELOG.md`: 和 Claude Code 版本 parity、remote control、background workflow、permission denial、MCP、hooks、session resume 等能力变化。
- `examples/session-stores/README.md`: SessionStore reference adapters、conformance、生产 checklist。
- `examples/session-stores/shared/conformance.ts`: SessionStore 行为合约。
- `examples/session-stores/{s3,redis,postgres}/src/`: 三种持久化实现。

## Useful Ideas For Our Harness

- session/transcript persistence 最好有正式 contract 和 conformance tests。
- append 失败不应静默，需要流式错误或监控信号。
- 存储后端需要明确 ordering、retention、pagination、TTL、clock skew、load latency 等工程问题。
- SDK changelog 是理解 runtime 能力演进的高密度线索。

## Caveats

当前本地副本不适合做 TypeScript SDK 的内部 agent loop 源码分析。若要深挖 TS SDK runtime，需要额外获取 npm package 源码或官方公开实现；否则建议以 Python SDK 为源码主线。

## Suggested Next Reading

读 `examples/session-stores/shared/conformance.ts`，提炼我们自己的 session-store contract 测试模板。
