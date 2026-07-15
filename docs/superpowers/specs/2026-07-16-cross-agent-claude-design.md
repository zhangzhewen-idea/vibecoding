# Claude Code 与 Codex 共用规则设计

## 背景

当前 `CLAUDE.md` 主要由 superpowers-zh 自动生成区块组成，`AGENTS.md` 则单独保存 Codex 的忽略规则适配。两份文件缺少明确的单一来源关系，容易让共用规则重复、漂移或产生优先级冲突。

## 目标

- 将 `CLAUDE.md` 定义为 Claude Code 与 Codex 的共用规则源。
- 让 Claude Code 继续通过原生方式读取 `CLAUDE.md`。
- 让 Codex 通过原生入口 `AGENTS.md` 加载 `CLAUDE.md`，再应用少量 Codex 专属适配。
- 保持 superpowers-zh 自动生成区块原样，避免后续更新覆盖手工修改。
- 共用规则只维护一份，平台入口文件不复制共用内容。

## 非目标

- 不重命名 `CLAUDE.md`。
- 不修改 `.claudeignore`、skill 内容或 superpowers-zh 的生成逻辑。
- 不处理当前工作区内与本任务无关的其他变更。
- 不要求两个平台使用相同的工具名称或 skill 加载命令。

## 文件职责

### `CLAUDE.md`

在自动生成标记之前增加手工维护的“AI Agent 共用规则”区块，明确：

- 本文件同时适用于 Claude Code 和 Codex。
- Claude Code 直接读取本文件；Codex 通过 `AGENTS.md` 引用本文件。
- 项目 skill 的规范来源是 `.claude/skills/`。
- Codex 通过 `.codex/skills` 链接访问同一组 skill。
- 各代理使用自身支持的方式加载 skill，不依赖特定工具名称。
- 平台专属规则只负责能力适配，不复制或改写共用规则。

现有 `superpowers-zh:begin` 与 `superpowers-zh:end` 之间的内容保持不变。

### `AGENTS.md`

作为 Codex 原生入口，仅保留：

- 执行任务前必须读取并遵循根目录 `CLAUDE.md`。
- 冲突时只允许 Codex 专属适配覆盖共用规则，不允许重复定义通用行为。
- Codex 需要人工遵守 `.claudeignore` 的现有说明。
- skill 目录映射说明：`.codex/skills` 指向 `.claude/skills`。

## 规则优先级

从高到低：

1. 用户在当前任务中的明确要求。
2. 当前代理平台的系统和安全约束。
3. `AGENTS.md` 中仅针对 Codex 的平台适配。
4. `CLAUDE.md` 中的项目共用规则。
5. skill 内部的默认流程。

`AGENTS.md` 不得借“平台适配”重复定义沟通、编码、测试或 Git 等共用规则。

## 兼容性处理

- 文件名仍为 `CLAUDE.md`，但正文明确其跨代理用途。
- Codex 不依赖自动发现 `CLAUDE.md`，而是由 `AGENTS.md` 显式引导读取。
- Claude Code 不需要理解 `AGENTS.md` 才能获得共用规则。
- `.codex/skills` 继续复用现有符号链接，不复制 skill 文件。
- 所有工具名差异由各 skill 的平台映射处理，共用入口不绑定 `Skill`、`Read`、`Bash` 等名称。

## 验证与验收

- `CLAUDE.md` 明确写明同时适用于 Claude Code 和 Codex。
- `AGENTS.md` 明确要求 Codex 读取 `CLAUDE.md`。
- superpowers-zh 自动生成区块内容和标记未改变。
- `.codex/skills` 仍是指向 `../.claude/skills` 的有效链接。
- 两份入口文件不存在重复或冲突的共用规则。
- 最终差异只包含本设计文档及 `CLAUDE.md`、`AGENTS.md` 的目标改动，不触碰其他工作区变更。
