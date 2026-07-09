# Codex 适配指南

## 安装

### Skill 文件位置

```
~/.codex/skills/acceptance-driven-development/SKILL.md
~/.codex/skills/acceptance-driven-development/IMPROVEMENT-GUIDE.md
~/.codex/skills/acceptance-driven-development/references/...
~/.codex/skills/project-experience/SKILL.md
```

或跨平台共享路径：`~/.agents/skills/`

### 配置多 Agent 支持

编辑 `~/.codex/config.toml`：

```toml
[features]
multi_agent = true
```

这是 Phase 4 Mode A 深度审查所需的——`spawn_agent` / `wait_agent` / `close_agent` 需要此配置。

### Vault 文件

同 Claude 版本，复制到你的 Obsidian Vault 的 `claude/` 目录。

---

## 工具映射

SKILL.md 中的 Claude Code 工具调用，在 Codex 中对应：

| SKILL.md 写法 | Codex 等价 |
|--------------|-----------|
| `Read file_path="path"` | `shell` → `cat path` |
| `Glob pattern="..."` | `shell` → `find` / `ls` |
| `Write file_path="path"` | `apply_patch` |
| `Edit file_path="path"` | `apply_patch` |
| `Bash command` | `shell` |
| `Skill("name")` | 技能原生加载，按说明执行 |
| `TaskCreate` / `TaskUpdate` | `update_plan` |
| 子 Agent（Phase 4.8 Mode A） | `spawn_agent` + `wait_agent` + `close_agent` |

---

## SKILL.md 中需要替换的文本

将以下内容替换为 Codex 等价写法：

### Phase 0 — 定位 Vault

```
# Claude 版本
Glob pattern="**/.obsidian"
Read file_path="<VAULT>/projects/<ProjectName>/验收标准.md"

# Codex 版本
shell: find ~ -maxdepth 4 -name ".obsidian" -type d
shell: cat "<VAULT>/projects/<ProjectName>/验收标准.md"
```

### Phase 4 — 子 Agent 审查

```
# Claude 版本
dispatch a review subagent

# Codex 版本
spawn_agent → wait_agent → close_agent
```

### Phase 4 — 任务跟踪

```
# Claude 版本
TaskCreate / TaskUpdate

# Codex 版本
update_plan
```

### 指令文件

```
# Claude 版本
CLAUDE.md / CLAUDE.local.md

# Codex 版本
AGENTS.md / AGENTS.override.md (~/.codex/AGENTS.md for global)
```

---

## Codex 特有注意事项

1. **子 Agent 配置**：必须在 `config.toml` 中启用 `multi_agent = true`，否则 `spawn_agent` 不可用
2. **文件操作**：Codex 使用 `apply_patch`（结构化 diff），不是直接 Write/Edit
3. **技能加载**：`Skill("name")` 在 Codex 中是原生加载，不需要显式调用工具——技能匹配后直接按说明执行
4. **AGENTS.md 路径**：Codex 从项目根目录向下遍历，合并所有 `AGENTS.md`（最大 32KiB）。在项目根目录放置你的项目级 `AGENTS.md`
5. **沙箱限制**：Codex App 可能在沙箱中运行，无法执行 git push/branch 操作。Phase 6 finishing 时需用 App 原生控件

---

## 快速验证

安装后在 Codex 中输入：

```
帮我实现一个简单功能，使用 ADD 技能
```

如果 Codex 自动进入 Phase 0（定位 Vault + 读取验收标准），说明安装成功。
