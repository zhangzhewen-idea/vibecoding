# Claude Code 与 Codex 共用规则实现计划

> **面向 AI 代理的工作者：** 必需子技能：使用 superpowers:subagent-driven-development（推荐）或 superpowers:executing-plans 逐任务实现此计划。步骤使用复选框（`- [ ]`）语法来跟踪进度。

**目标：** 将 `CLAUDE.md` 建立为 Claude Code 与 Codex 的共用规则源，并让 `AGENTS.md` 只承担 Codex 原生入口和平台适配职责。

**架构：** 在 `CLAUDE.md` 的 superpowers-zh 自动生成区块之前增加手工维护的跨代理说明，自动生成内容保持字节级不变。`AGENTS.md` 显式要求 Codex 加载 `CLAUDE.md`，并保留忽略规则、skill 路径和优先级等 Codex 专属适配。

**技术栈：** Markdown、Git、POSIX shell、`rg`、`awk`、`shasum`

---

## 文件结构

- 修改：`CLAUDE.md`，维护 Claude Code 与 Codex 共用规则，并保留 superpowers-zh 自动生成区块。
- 修改：`AGENTS.md`，维护 Codex 原生入口和平台适配，不复制共用规则。
- 参考：`docs/superpowers/specs/2026-07-16-cross-agent-claude-design.md`，作为需求和验收依据。

### 任务 1：为 `CLAUDE.md` 增加跨代理共用入口

**文件：**

- 修改：`CLAUDE.md:1`
- 测试：`CLAUDE.md`

- [ ] **步骤 1：记录并校验自动生成区块基线**

运行：

```bash
awk '/<!-- superpowers-zh:begin/{capture=1} capture{print} /<!-- superpowers-zh:end -->/{capture=0}' CLAUDE.md | shasum -a 256
```

预期输出：

```text
d982afcbdedf5478c45798a9c30c0b0c669a66c50bfac0f7e4004d18c6f7ad17  -
```

- [ ] **步骤 2：确认跨代理共用入口尚不存在**

运行：

```bash
rg -n '^# AI Agent 共用规则$' CLAUDE.md
```

预期：退出码为 `1`，没有输出。

- [ ] **步骤 3：在自动生成区块之前加入共用规则**

在 `CLAUDE.md` 第一行之前加入以下完整内容：

```markdown
# AI Agent 共用规则

本文件是本项目面向 Claude Code 与 Codex 的共用规则源。

## 加载方式

- Claude Code 直接读取并遵循本文件。
- Codex 通过根目录 `AGENTS.md` 加载并遵循本文件，再应用其中的 Codex 专属适配。

## Skill 共用约定

- 项目 skill 的唯一来源是 `.claude/skills/`。
- `.codex/skills` 指向 `.claude/skills/`，Codex 与 Claude Code 共用同一组 skill。
- 代理应使用当前平台原生支持的方式加载 skill，不得假设 `Skill`、`Read`、`Bash` 等特定工具名始终存在。

## 规则边界与优先级

- 共用规则只在本文件维护；平台入口文件只补充平台能力差异，不复制共用规则。
- 规则冲突时，按“用户当前明确要求 > 平台系统与安全约束 > 平台专属适配 > 本文件共用规则 > skill 默认流程”的顺序处理。
- 下方标记为自动生成的 superpowers-zh 区块不得手工修改。

```

- [ ] **步骤 4：验证共用入口存在且自动生成区块未变**

运行：

```bash
rg -n '^# AI Agent 共用规则$|^## 加载方式$|^## Skill 共用约定$|^## 规则边界与优先级$' CLAUDE.md
```

预期：四个标题各出现一次。

运行：

```bash
awk '/<!-- superpowers-zh:begin/{capture=1} capture{print} /<!-- superpowers-zh:end -->/{capture=0}' CLAUDE.md | shasum -a 256
```

预期输出仍为：

```text
d982afcbdedf5478c45798a9c30c0b0c669a66c50bfac0f7e4004d18c6f7ad17  -
```

### 任务 2：将 `AGENTS.md` 收敛为 Codex 平台入口

**文件：**

- 修改：`AGENTS.md:1`
- 测试：`AGENTS.md`

- [ ] **步骤 1：确认 Codex 加载指令尚不存在**

运行：

```bash
rg -n '^执行任何任务前，必须先读取并遵循根目录 `CLAUDE.md`。$' AGENTS.md
```

预期：退出码为 `1`，没有输出。

- [ ] **步骤 2：使用精简的平台入口内容替换现有文件**

将 `AGENTS.md` 替换为以下完整内容：

```markdown
# Codex 项目入口

执行任何任务前，必须先读取并遵循根目录 `CLAUDE.md`。

## 适配边界

- 本文件只维护 Codex 专属适配，不复制 `CLAUDE.md` 中的共用规则。
- 发生冲突时，只允许 Codex 的平台系统、安全约束和能力差异覆盖共用规则。
- 项目 skill 通过 `.codex/skills` 访问；该路径指向 `../.claude/skills`。
- Codex 使用当前环境支持的原生方式加载 skill，并按 skill 内的 Codex 工具映射执行。

## 忽略规则

本项目复用根目录 `.claudeignore` 作为 Codex 的忽略规则来源。

- Codex 在执行文件读取、搜索、分析或编辑前，必须先查看 `.claudeignore`。
- `.claudeignore` 中匹配的路径，不得主动读取、搜索、分析、修改或输出其内容。
- 如果用户明确要求处理被 `.claudeignore` 匹配的文件，先说明该路径被忽略规则覆盖，并等待用户确认。
- `.claudeignore` 是项目约定，不代表 Codex 原生支持；Codex 必须通过本文件人工遵守。
```

- [ ] **步骤 3：验证 Codex 入口、适配边界和忽略规则**

运行：

```bash
rg -n '^# Codex 项目入口$|^执行任何任务前，必须先读取并遵循根目录 `CLAUDE.md`。$|^## 适配边界$|^## 忽略规则$' AGENTS.md
```

预期：四个目标行各出现一次。

运行：

```bash
test "$(readlink .codex/skills)" = "../.claude/skills"
```

预期：退出码为 `0`，没有输出。

### 任务 3：执行跨文件验收并提交目标改动

**文件：**

- 验证：`CLAUDE.md`
- 验证：`AGENTS.md`

- [ ] **步骤 1：检查 Markdown 差异格式**

运行：

```bash
git diff --check -- CLAUDE.md AGENTS.md
```

预期：退出码为 `0`，没有输出。

- [ ] **步骤 2：确认共用规则没有在 `AGENTS.md` 中重复**

运行：

```bash
! rg -n '^## (加载方式|Skill 共用约定|规则边界与优先级)$' AGENTS.md
```

预期：退出码为 `0`，没有输出。

- [ ] **步骤 3：审查目标文件最终差异**

运行：

```bash
git diff -- CLAUDE.md AGENTS.md
```

预期：`CLAUDE.md` 只在自动生成区块之前增加共用规则；`AGENTS.md` 只包含 Codex 入口与适配；其他文件不出现在输出中。

- [ ] **步骤 4：提交前展示变更摘要**

运行：

```bash
git diff --stat -- CLAUDE.md AGENTS.md
```

预期：只列出 `CLAUDE.md` 与 `AGENTS.md`。将摘要展示给用户，不包含工作区内其他已暂存或未提交变更。

- [ ] **步骤 5：仅提交目标文件**

运行：

```bash
git add CLAUDE.md AGENTS.md
git commit --only CLAUDE.md AGENTS.md -m "docs: 统一 AI 代理项目规则"
```

预期：创建一个只包含 `CLAUDE.md` 与 `AGENTS.md` 的提交，其他工作区变更保持原状态。
