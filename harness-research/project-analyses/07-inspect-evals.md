# Inspect Evals

Local source: `../../repos/inspect_evals`

## What It Is

Inspect Evals 是 Inspect AI 的社区评测集合。它的价值不在 runtime，而在大量 eval 的组织方式、依赖管理、文档生成、贡献/注册流程和任务实现样例。

## Harness Architecture

它复用 Inspect AI runtime，自己主要提供：

`eval implementation packages -> docs listing/register metadata -> inspect eval CLI -> Inspect AI logs/viewer`

2026 年后社区贡献开始转向 `/register/`，即仓库更多承担 eval registry/catalog 的角色，而不是把所有实现都内嵌进主仓。

## Key Source Entry Points

- `README.md`: usage、eval listing、register 流程、硬件/依赖说明。
- `src/inspect_evals/`: 已内置的 eval 实现集合。
- `register/README.md`、`EVAL_REGISTER.md`: 新贡献流程与元数据注册机制。
- 各 eval 子目录 README: 任务依赖、运行命令、数据说明。

## Useful Ideas For Our Harness

- eval collection 要有“可运行说明 + 元数据 + 依赖边界 + 文档生成”四件套。
- 社区 eval 最终会遇到依赖膨胀，注册外部 repo 是降低主仓负担的办法。
- 任务列表本身可作为 benchmark catalog，帮助我们按能力维度分类。

## Caveats

它不是独立 harness。分析源码时应把 Inspect Evals 看作 Inspect AI 的 eval asset layer。

## Suggested Next Reading

挑 coding 类 eval，如 `apps`、`agent_bench`、`bigcodebench`、`core_bench`，看它们如何把任务转成 Inspect AI `Task`。
