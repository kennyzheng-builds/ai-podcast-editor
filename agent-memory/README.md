# Agent Memory

这个目录存放 AI 助手（Capy）的持久化记忆文件，用于在不同设备、不同 session 之间保持上下文连续性。

## 文件说明

| 文件 | 用途 |
|---|---|
| `MEMORY.md` | 长期记忆：项目状态、技术决策、API Keys 状态 |
| `USER.md` | 用户偏好：沟通风格、技术背景、工作习惯 |
| `memory/YYYY-MM-DD.md` | 每日 session 日志 |

## 在新设备恢复上下文

在新电脑上 `git clone` 项目后，把这些文件复制到 agent 目录：

```bash
cp agent-memory/MEMORY.md ~/.happycapy/agents/capy-default/MEMORY.md
cp agent-memory/USER.md ~/.happycapy/agents/capy-default/USER.md
mkdir -p ~/.happycapy/agents/capy-default/memory
cp agent-memory/memory/*.md ~/.happycapy/agents/capy-default/memory/
```

这样 AI 助手就能在新设备上恢复完整上下文。
