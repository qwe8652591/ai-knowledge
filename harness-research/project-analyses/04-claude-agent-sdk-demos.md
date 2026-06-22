# Claude Agent SDK Demos

Local source: `../../repos/claude-agent-sdk-demos`

## What It Is

这是 Anthropic 的 Agent SDK demo 集合，重点不是框架本体，而是展示如何把 Claude Agent SDK 嵌入真实产品：邮件 agent、Excel demo、research agent、chat app、resume generator 等。

## Harness Architecture

这些 demo 的共同模式是：

`frontend/user event -> app session object -> SDK client/query -> streaming SDK messages -> websocket/UI state -> DB/session resume`

其中 email-agent 的 `Session` 对象尤其典型：它维护 SDK session id、串行化用户消息、WebSocket subscribers、DB 持久化，并把 assistant/tool/result/system/user 消息转换成前端可消费事件。

## Key Source Entry Points

- `README.md`: demo 列表与定位。
- `email-agent/ccsdk/session.ts`: 产品 session 管理、SDK resume、消息队列、WebSocket streaming。
- `research-agent/`: research workflow 的 agent 应用结构。
- `excel-demo/agent/`: tool-heavy 办公场景示例。

## Useful Ideas For Our Harness

- 产品层需要自己的 session queue，避免用户连续输入和 agent 当前 query 互相打架。
- SDK 原始消息流需要转换成 UI 友好的 tool/result/system/message 事件。
- session id/resume 是多轮交互的核心，不只是日志字段。
- 真实产品会关心“忙/空闲”状态、部分输出、工具渲染、错误恢复、订阅者广播。

## Caveats

这些 demo 明确不是生产库，很多设计是应用胶水层。适合学习 SDK 嵌入方式，不适合作为安全/权限/沙箱实现参考。

## Suggested Next Reading

继续深读 `email-agent/ccsdk/session.ts`，把消息转换和 session resume 流程画成 sequence diagram。
