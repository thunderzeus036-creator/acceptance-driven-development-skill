# Skill Improvement Guide — 给未来的 Agent 和开发者

> 本文件记录 acceptance-driven-development Skill 的设计原则、已验证的防线、改进时的注意事项。如果你要修改这个 Skill，请先读完本文件。

---

## 这个 Skill 解决什么问题

Agent 开发代码时会跳过质量流程——自审、验证、标记。尤其在以下场景：
- **「简单」改动**：Agent 认为一行代码不需要流程
- **调试模式**：Agent 进入「分析→修复」快速路径后忘记流程
- **用户措辞**：Agent 把「用户描述了问题」等同于「用户确认了方案」
- **长会话**：上下文变长后，Skill 规则从 Agent 的注意力中「下沉」

Skill 的核心循环是：`AC 表有 [ ] → 做 → 验 → 标记 → 还有 [ ]？→ 继续`。所有防线都是围绕这个循环加固的，不是在核心上叠加新功能。

---

## 已验证的防线（不要轻易删除）

| 防线 | 位置 | 验证时间 | 为什么有效 |
|------|------|---------|-----------|
| `<EXTREMELY-IMPORTANT>` 标签 | FIRST RULE | 2026-07 | 比普通 Markdown 标题权重更高，参考 using-superpowers |
| 合理化借口表 | FIRST RULE | 2026-07 | Agent 会用每种理由跳过规则，提前封堵每种理由 |
| Phase 3.5 硬门禁（等确认） | Phase 3.5 出口 | 2026-07 | Agent 会把「用户描述了问题」等同于「确认」，硬门禁强制等待 |
| Phase 4 出口门禁 | Phase 4 末尾 | 2026-07 | Agent 会跳过 Phase 4.8 审查直接标记 [x] |
| Phase 5 Mode B 检查 | Phase 5 | 2026-07 | Mode B 项可能漏标 [x]，Phase 5 二次检查 |
| Phase 6 硬门禁 | Phase 6 | 2026-06 | Agent 会在有 [ ] 时说「基本完成了」 |
| Mode A/B 双模式 | Phase 4 | 2026-07 | Agent 对单个改动不愿走全量扫描，Mode B 解决这个问题 |
| 里程碑审查 | Mode B | 2026-07 | 长会话中多次轻量自审遗漏跨项交互，周期性深度审查兜底 |

---

## 改进时的原则

### 1. 加规则前，先确认问题不是执行层的
如果 Agent 不遵守规则，先检查：
- 规则是否在 Skill 的可见位置（越靠前越可能被读到）
- 规则是否有 `<EXTREMELY-IMPORTANT>` 标签
- 是否有对应的合理化借口封堵

如果以上都有但 Agent 仍然跳过，这是 **Agent 认知架构的根本限制**，不是 Skill 能解决的。加更多规则只会让 Skill 更长，加剧遗忘。

### 2. 不要在 Phase 3.5 的豁免列表里加太多例外
每加一个「不需要 Phase 3.5」的例外，Agent 就多一条合理化路径。「性能优化不需要 Phase 3.5」就是被加回来的——Agent 会扩大解释任何例外。

### 3. Mode B 的边界要清晰
Mode B 是轻量模式，上限是 2 条 AC——≥3 条升级为 Mode A。上限曾经是 3 条，实战发现 Agent 会把中型改动（3 条）塞进轻量模式，收紧后效果更好。

### 4. 审查模式必须和执行模式一致
Mode A 执行 → Mode A 审查（qt-cpp-review 子 Agent）。Mode B 执行 → Mode B 审查（自审）。混用会导致批量执行时审查力度不足。

### 5. 每个 Phase 之间必须有显式引导
Agent 会在两个 Phase 之间迷路。每个 Phase 的出口必须有明确的「下一步是什么」指令。不要假设 Agent 会自动按顺序执行。

---

## 上下文长度的影响

当对话变长时（>50 轮），以下现象会发生：
- Agent 开始跳过 Skill 规则，尤其是 Phase 3.5 的讨论+确认
- FIRST RULE 从 Agent 的注意力中「下沉」
- Agent 会把「用户描述了问题」等同于「确认方案」

**应对方式（不在 Skill 里加规则）：**
- 用户在 Agent 说「修好了」后追问「走了 Mode B 吗？」——这是最有效的防线
- 失败阈值（3 次修不过就停下来）——换方案比继续修补更高效
- 如果用户有代码分析工具（如 codebase-memory MCP 的 trace_path），影响分析可以用工具替代手动 grep，更精准地发现跨文件的间接调用
- 未来可以考虑在 CLAUDE.md 里加一条简短提醒，让每次对话开始时 Agent 都看到

---

## 改进 Skill 的工作流程

```
1. 发现问题（用户或 Agent 报告）
   ↓
2. 分析：是规则缺失、规则位置不好、还是 Agent 合理化？
   ↓
3. 选择修复方式：
   - 规则缺失 → 加规则
   - 位置不好 → 移到更靠前的位置
   - Agent 合理化 → 加借口封堵 + <EXTREMELY-IMPORTANT> 标签
   - 根本限制 → 不改 Skill，靠用户追问
   ↓
4. 修复后：更新 Vault 笔记的设计决策表（记录「为什么」）
   ↓
5. 同步分享包：cp SKILL.md → 桌面/自动化开发skill/
```

---

## 文件清单

| 文件 | 用途 | 修改频率 |
|------|------|---------|
| `SKILL.md` | 主 Skill，Agent 加载时读取 | 高（每次发现问题都改） |
| `IMPROVEMENT-GUIDE.md` | 本文件，给未来的改进者 | 低（设计原则稳定后很少改） |
| `references/framework-review-checklist.md` | 框架自审清单 | 低（按需增加框架） |
| `references/qt-cpp-review/` | Qt 官方审查技能 | 低（跟随 Qt 官方更新） |
| Vault 笔记 `Acceptance-Driven-Development Skill.md` | 设计决策记录 | 中（每次重大改动同步） |
| 分享包 `桌面/自动化开发skill/` | 给其他人用的安装包 | 中（每次改 SKILL.md 同步） |
