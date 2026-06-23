# AGENTS.md

本文件为 AI 编码 agent 提供项目导航，帮助快速理解项目约定与结构。

## 项目概述

本项目是一个 **Claude Code Skills（技能）合集插件仓库**，用于发布可被 Claude Code 安装和使用的技能（Skills）。每个技能是一个自包含的目录，核心是 `SKILL.md` 文件，向 agent 注入特定领域知识、工作流或操作规范。

项目本身**不包含可执行代码、构建工具或依赖管理器**——它是一个纯配置与文档型仓库。"构建"产物即为目录结构本身，由 Claude Code 直接加载。

## 目录结构

```
.
├── .claude-plugin/
│   ├── plugin.json          # 插件清单：名称、版本、技能路径注册
│   └── marketplace.json     # 技能市场（marketplace）发布元数据
├── skills/
│   └── <skill-name>/
│       ├── SKILL.md         # 必需：技能定义（YAML front matter + Markdown 指令）
│       └── references/      # 可选：参考文档
├── README.md                # 人类向的项目说明
└── AGENTS.md                # 本文件
```

### 当前已收录技能

| 技能 | 路径 | 说明 |
|------|------|------|
| `doc-first` | `skills/doc-first/SKILL.md` | 文档优先原则：要求 agent 先写文档、后写代码，代码变更必须同步更新文档 |
| `spec-first` | `skills/spec-first/SKILL.md` | 需求澄清与原型确认：要求 agent 先澄清需求、确认原型方案，获得用户认可后再编码 |
| `bun-pnpm` | `skills/bun-pnpm/SKILL.md` | Bun 项目使用 pnpm 管理依赖：依赖管理（install/add/remove）用 pnpm，运行时（脚本执行、测试、服务启动）用 bun |
| `done-check` | `skills/done-check/SKILL.md` | 开发完成验收：要求 agent 在宣告任务完成前执行系统性验收（需求回溯、测试、手动验证、diff 自审、回归排查、文档同步），按 CLI / Web / Server 区分验收方式 |



## 关键配置文件

### `.claude-plugin/plugin.json`

插件主清单文件。核心字段：

- `name`、`version`、`description`：插件标识与描述
- `skills`：**数组**，列出各技能的相对路径（如 `["./skills/figma-web-restore"]`）

**添加新技能时，必须在此文件的 `skills` 数组中注册路径。**

### `.claude-plugin/marketplace.json`

技能市场发布元数据，遵循 schema `https://json.schemastore.org/claude-code-marketplace.json`。包含 owner 信息、插件列表（含每个插件的 source、version、keywords、category 等）。此文件面向 Claude Code marketplace 分发，非运行时加载逻辑。

### `skills/<name>/SKILL.md`

技能定义文件，由两部分组成：

1. **YAML front matter**：`name`（技能名）与 `description`（触发条件与职责描述），用于让 agent 判断何时使用该技能
2. **Markdown 正文**：面向 agent 的详细指令、流程、检查清单等

## 添加新技能的流程

1. 在 `skills/` 下创建新目录，目录名即技能名（kebab-case）。
2. 创建 `SKILL.md`，包含 YAML front matter（`name`、`description`）与 Markdown 正文指令。
3.（可选）添加 `references/` 子目录存放参考文档。
4. 在 `.claude-plugin/plugin.json` 的 `skills` 数组中添加该技能的相对路径。
5.（如需发布到市场）在 `.claude-plugin/marketplace.json` 的 `plugins` 数组中添加对应条目。
6. 更新 `README.md` 的已收录技能表格。

## 构建与测试

- **无构建步骤**：项目不含编译、打包流程，无 `package.json` / `pyproject.toml` / `Cargo.toml` 等构建配置。
- **无自动化测试**：项目没有测试套件。验证方式为人工确认：
  - `SKILL.md` 的 YAML front matter 格式正确
  - `plugin.json` 为合法 JSON 且 `skills` 路径指向真实存在的目录
  - `marketplace.json` 符合其声明的 JSON Schema
  - 可用 `python3 -m json.tool` 或 `jq .` 对 JSON 文件做语法校验

## 编码与写作约定

- **语言**：项目文档与技能内容以**中文**为主，技术术语保留英文原文（如 `SKILL.md`、front matter）。
- **文档优先原则**：本项目自身的 `doc-first` 技能即要求 agent 遵循"先文档后代码"的工作流。修改技能定义或配置后，应同步更新 `README.md` 与本文件。
- **技能命名**：使用 kebab-case（连字符小写），如 `doc-first`、`figma-web-restore`。
- **JSON 文件**：保持 2 空格缩进，与现有文件风格一致。

## Git 约定

- 主分支：`main`
- Commit message 使用中文前缀风格，如 `init:`（见首次提交 `init: Claude Code skills plugin repo`）
- 文档更新提交建议使用 `docs:` 类型前缀

## 许可证

MIT（见 `README.md` 声明）。
