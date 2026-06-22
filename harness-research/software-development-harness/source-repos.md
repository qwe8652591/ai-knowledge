# Downloaded Source Repositories

本轮源码下载时间：2026-06-22

源码根目录：

`D:\code\local\research\harness\repos\software-development-harness`

下载方式：`git clone --depth 1 --filter=blob:none`

| 项目 | 本地目录 | branch | commit | URL |
| --- | --- | --- | --- | --- |
| OpenAI Codex Action | `openai-codex-action` | `main` | `9cdb6f3` | https://github.com/openai/codex-action |
| Anthropic Claude Code Action | `anthropic-claude-code-action` | `main` | `2fee155` | https://github.com/anthropics/claude-code-action |
| Vibe Kanban | `bloop-vibe-kanban` | `main` | `4deb7ec` | https://github.com/BloopAI/vibe-kanban |
| Claude Squad | `smtg-claude-squad` | `main` | `5a604f7` | https://github.com/smtg-ai/claude-squad |
| Crystal | `stravu-crystal` | `main` | `1e18e0b` | https://github.com/stravu/crystal |
| Task Master AI | `task-master-ai` | `main` | `c0c98d3` | https://github.com/eyaltoledano/claude-task-master |
| BMAD Method | `bmad-method` | `main` | `e600181` | https://github.com/bmad-code-org/BMAD-METHOD |
| SuperClaude Framework | `superclaude-framework` | `master` | `226c45c` | https://github.com/SuperClaude-Org/SuperClaude_Framework |
| Ruflo / Claude Flow | `ruflo-claude-flow` | `main` | `9c28fe0` | https://github.com/ruvnet/ruflo |
| Superpowers | `obra-superpowers` | `main` | `896224c` | https://github.com/obra/Superpowers |
| PR-Agent | `pr-agent` | `main` | `3e9dd1d` | https://github.com/qodo-ai/pr-agent |

## First Reading Targets

| 项目 | 优先阅读文件 |
| --- | --- |
| `openai-codex-action` | `README.md`, `action.yml`, `docs/security.md`, `examples/` |
| `anthropic-claude-code-action` | `README.md`, `action.yml`, `docs/solutions.md`, `docs/security.md`, `docs/configuration.md` |
| `bloop-vibe-kanban` | `README.md`, `crates/worktree-manager/`, `crates/executors/`, `crates/review/`, `crates/server/` |
| `smtg-claude-squad` | `README.md`, Go command/session/worktree modules |
| `stravu-crystal` | `README.md`, migration note to Nimbalyst |
| `task-master-ai` | `README.md`, `docs/`, `apps/mcp/`, `apps/cli/`, `packages/tm-core/` |
| `bmad-method` | `README.md`, `tools/installer/`, workflow/agent definitions |
| `superclaude-framework` | `README.md`, `src/superclaude/commands/`, `src/superclaude/agents/`, `src/superclaude/hooks/` |
| `ruflo-claude-flow` | `README.md`, `v3/@claude-flow/codex/README.md`, `plugins/`, `v3/@claude-flow/hooks/`, `v3/@claude-flow/mcp/` |
| `obra-superpowers` | `README.md`, skills directory, harness-specific install docs |
| `pr-agent` | `README.md`, `action.yaml`, `pr_agent/settings/configuration.toml`, provider adapters |

## Notes

- Vibe Kanban README says the project is sunsetting; it is still valuable as a design reference for workspace/worktree/product UX.
- Crystal README says Crystal is deprecated and replaced by Nimbalyst; treat this clone as historical reference.
- Ruflo is large and ambitious; claims around memory/swarm/federation should be verified in code before production adoption.
