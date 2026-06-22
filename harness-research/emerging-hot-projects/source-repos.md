# 本轮源码下载清单

下载目录：`repos/emerging-hot-projects/`  
下载方式：`git clone --depth 1 --filter=blob:none`  
记录时间：2026-06-22

| 项目 | URL | 本地目录 | Branch | Commit | 状态 |
| --- | --- | --- | --- | --- | --- |
| openclaw/openclaw | https://github.com/openclaw/openclaw.git | `repos/emerging-hot-projects/openclaw` | main | `c3b1e926` | 完成 |
| goldmar/openclaw-code-agent | https://github.com/goldmar/openclaw-code-agent.git | `repos/emerging-hot-projects/openclaw-code-agent` | main | `0cb328c` | 完成 |
| NousResearch/hermes-agent | https://github.com/NousResearch/hermes-agent.git | `repos/emerging-hot-projects/hermes-agent` | main | `f79e0a7` | 完成 |
| NVIDIA/NemoClaw | https://github.com/NVIDIA/NemoClaw.git | `repos/emerging-hot-projects/nemo-claw` | main | `f8d9c4a` | 完成 |
| NVIDIA/OpenShell | https://github.com/NVIDIA/OpenShell.git | `repos/emerging-hot-projects/openshell` | main | `f23c2c8` | 完成 |
| TheAiSingularity/hermesclaw | https://github.com/TheAiSingularity/hermesclaw.git | `repos/emerging-hot-projects/hermesclaw` | main | `6479c22` | 完成 |
| johannesjo/parallel-code | https://github.com/johannesjo/parallel-code.git | `repos/emerging-hot-projects/parallel-code` | main | `7998648` | 完成 |
| sipyourdrink-ltd/bernstein | https://github.com/sipyourdrink-ltd/bernstein.git | `repos/emerging-hot-projects/bernstein` | main | `321c96f` | 部分 checkout：Windows 长路径导致部分文件未落盘，核心 docs/src/tests 可读 |
| nwiizo/ccswarm | https://github.com/nwiizo/ccswarm.git | `repos/emerging-hot-projects/ccswarm` | master | `53f04fe` | 完成 |
| truffle-ai/dexto | https://github.com/truffle-ai/dexto.git | `repos/emerging-hot-projects/dexto` | main | `5500bad` | 完成 |
| Enderfga/claw-orchestrator | https://github.com/Enderfga/claw-orchestrator.git | `repos/emerging-hot-projects/claw-orchestrator` | main | `711b490` | 完成 |
| jnMetaCode/agency-agents-zh | https://github.com/jnMetaCode/agency-agents-zh.git | `repos/emerging-hot-projects/agency-agents-zh` | main | `80c30a0` | 完成 |
| VoltAgent/awesome-openclaw-skills | https://github.com/VoltAgent/awesome-openclaw-skills.git | `repos/emerging-hot-projects/awesome-openclaw-skills` | main | `8d0a5d4` | 完成 |
| mergisi/awesome-openclaw-agents | https://github.com/mergisi/awesome-openclaw-agents.git | `repos/emerging-hot-projects/awesome-openclaw-agents` | main | `05820c5` | 完成 |

## 后续建议

如果要跑 Bernstein 的测试或完整读取所有 adapter，建议在 WSL/Linux 下重新 clone，或先启用 Windows long paths：

```powershell
git config --global core.longpaths true
```

然后重新 clone 到较短路径，例如 `D:\r\bernstein`。

