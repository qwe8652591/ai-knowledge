# ai-knowledge

AI agent harness / software-development harness 研究资料库。

当前内容主要来自 `D:\code\local\research\harness` 的本地研究项目，包含：

- OpenAI、Anthropic、ChatGPT/Codex、Claude Code 等对 harness 的定义与实践整理；
- agent runtime harness、evaluation harness、software-development harness 的分类；
- OpenAI Codex、Claude Agent SDK、Inspect AI、SWE-bench、Terminal-Bench、OpenHands、AutoGen、Google ADK 等项目源码分析；
- Claude Code / Codex 之上的软件开发层 harness 项目比较；
- OpenClaw、Hermes Agent、NemoClaw、OpenShell、Dexto、Bernstein、ccswarm、Claw Orchestrator 等最新/热门项目补充研究。

## 目录

- [harness-research/README.md](harness-research/README.md)：总入口。
- [harness-research/project-analyses/README.md](harness-research/project-analyses/README.md)：第一批源码项目逐项分析。
- [harness-research/software-development-harness/README.md](harness-research/software-development-harness/README.md)：软件开发层 harness 专题。
- [harness-research/emerging-hot-projects/README.md](harness-research/emerging-hot-projects/README.md)：OpenClaw、Hermes Agent 等最新/热门项目补充研究。

## 源码缓存说明

研究过程中下载的第三方源码仓库保存在本地 `repos/` 目录中。该目录包含大量独立 git 仓库，不直接提交到本仓库，以避免把第三方项目作为嵌套仓库或错误 submodule 提交。

如需复现源码下载，请参考：

- [harness-research/source-repos.md](harness-research/source-repos.md)
- [harness-research/repo-download-manifest.json](harness-research/repo-download-manifest.json)
- [harness-research/software-development-harness/source-repos.md](harness-research/software-development-harness/source-repos.md)
- [harness-research/software-development-harness/repo-download-manifest.json](harness-research/software-development-harness/repo-download-manifest.json)
- [harness-research/emerging-hot-projects/source-repos.md](harness-research/emerging-hot-projects/source-repos.md)
- [harness-research/emerging-hot-projects/repo-download-manifest.json](harness-research/emerging-hot-projects/repo-download-manifest.json)
